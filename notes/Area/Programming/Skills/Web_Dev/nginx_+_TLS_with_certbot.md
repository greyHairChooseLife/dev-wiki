# 󰏢 nginx + TLS with certbot


## Complete TLS Integration Plan

### **Architecture Overview**

- Use nginx:alpine base with certbot installed
- Implement dual-mode operation: HTTP-only initially, then HTTPS after cert generation
- Auto-renewal via cron job inside container
- Persistent certificate storage via Docker volumes

### **File Structure Changes**

- ```bash
  docker/nginx/
  ├── Dockerfile
  ├── nginx.conf (base config)
  ├── ssl.conf (HTTPS virtual host)
  ├── init-ssl.sh (certificate management script)
  └── certbot-renew.sh (renewal cron job)
  ```

### **Flow Process**

**1. Container Startup Flow:**
- Start nginx in HTTP-only mode
- Check if certificates exist for domain
- If no certs: use Certbot HTTP-01 challenge to obtain certificates
- If certs exist: validate expiry, renew if needed
- Switch nginx to HTTPS mode with SSL config
- Setup cron for auto-renewal (every 12 hours)

**2. Certificate Management:**
- Initial acquisition via HTTP challenge (requires domain pointing to server)
- Automatic renewal 30 days before expiry
- Graceful nginx reload after renewal
- **Fallback to self-signed certs for development**
  - Used when no public domain available (local development)
  - Enables HTTPS testing without Let's Encrypt requirements
  - Generated with: `openssl req -x509 -nodes -days 365 -newkey rsa:2048`
  - This is neede when the dev server needs:
    >
    > - **Browser Security Features:**
    >   ```javascript
    >   // These only work over HTTPS (even in dev)
    >   navigator.geolocation.getCurrentPosition()  // Geolocation
    >   navigator.serviceWorker.register()         // Service Workers  
    >   navigator.mediaDevices.getUserMedia()      // Camera/Microphone
    >   // Secure cookies with SameSite=None
    >   ```
    >
    > - **Third-party Integration Testing:**
    >   - OAuth providers often require HTTPS callbacks
    >   - Payment processors (Stripe, PayPal) require HTTPS
    >   - Some APIs reject HTTP origins



**3. Docker Compose Updates:**
- Add volumes for `/etc/letsencrypt` and `/var/lib/letsencrypt`
- Environment variables for domain name and email
- Health checks for HTTPS endpoints

**4. Environment Variables Needed:**
- `DOMAIN_NAME` - Your domain for certificates
- `LETSENCRYPT_EMAIL` - Email for Let's Encrypt registration
- `SSL_MODE` - Certificate mode selection:
  - `production`: Real trusted certificates (5 certs/week limit)
  - `staging`: Test certificates with relaxed limits (30k/week) - browser shows "Not Secure"
  - `development`: Self-signed certificates for local testing

**5. Production Deployment Considerations:**
- Domain must be pointed to server before first run
- Port 80 must be accessible for HTTP challenge
- **Staging mode for testing to avoid rate limits**
  - Always test automation with staging first
  - Staging certs are real but signed by test CA ("Fake LE Intermediate X1")
  - Prevents hitting production rate limits during setup/debugging

### **Certificate Verification Methods**

**Check Certificate Generation:**
```bash
# Verify files exist
ls -la /etc/letsencrypt/live/$DOMAIN_NAME/
# Should show: cert.pem, chain.pem, fullchain.pem, privkey.pem
```

**Verify Certificate Type:**
```bash
# Check certificate issuer
openssl x509 -in /etc/letsencrypt/live/$DOMAIN_NAME/cert.pem -text -noout | grep Issuer
# Staging: "Fake LE Intermediate X1"
# Production: "R3" or "Let's Encrypt Authority"
```

**Test HTTPS Connection:**
```bash
# Works with any certificate type
curl -k https://yourdomain.com
# -k flag ignores certificate trust issues for staging/self-signed
```

**Browser Verification:**
- **Production**: Shows lock icon (trusted)
- **Staging**: Shows "Not Secure" but encryption works
- **Dev(Self-signed)**: Shows "Not Secure" with certificate warning

