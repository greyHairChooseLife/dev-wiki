To restart the sound-related service using `systemctl` on Manjaro Linux, you typically need to restart the PulseAudio or PipeWire service, depending on which one you are using. Here are the steps for both scenarios:

### Restarting PulseAudio:

1. Open a terminal window.

2. Run the following command to restart the PulseAudio service:

   ```bash
   systemctl --user restart pulseaudio
   ```

   If you have administrative (root) privileges and want to restart PulseAudio system-wide, you can use the following command instead:

   ```bash
   sudo systemctl restart pulseaudio
   ```

3. After running the command, PulseAudio will be restarted, and any sound-related issues may be resolved.

### Restarting PipeWire:

1. Open a terminal window.

2. Run the following command to restart the PipeWire service:

   ```bash
   systemctl --user restart pipewire
   ```

   To restart PipeWire system-wide with administrative privileges, use:

   ```bash
   sudo systemctl restart pipewire
   ```

3. After running the command, PipeWire will be restarted, and any sound-related issues may be resolved.

Choose the appropriate command based on whether you are using PulseAudio or PipeWire as your sound server. Restarting the sound service using `systemctl` is a user-friendly and straightforward way to address sound-related problems on your Manjaro Linux system.
