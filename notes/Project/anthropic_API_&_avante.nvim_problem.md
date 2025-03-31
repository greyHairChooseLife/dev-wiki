# 󰏢 anthropic API & avante.nvim problem



[github issue](https://github.com/yetone/avante.nvim/issues/1781)


1. 문제 인지 2025. 03. 31. (월) 15:40:02 KST
   : 실사용중이 아닌데도 API 비용이 $5씩 2회 연달아 출금됨.

   - 1차: _2025. 03. 31. (월) 14:10:00 KST_
   - 2차: _2025. 03. 31. (월) 15:09:00 KST_

2. 크레딧 자동 충전 중단

3. API 키 사용중지
   : CBPM-desktop


## history

> anthropic console에서 정확한 input/output을 확인할 수는 없다. 다만 in/out 토큰량 정보는 있어서
> 추정해본다.


| Time (GMT+9)        | ID                           | Model                      | Workspace | Input Tokens | Output Tokens | Type      |
|---------------------|------------------------------|----------------------------|-----------|--------------|---------------|-----------|
| 2025-03-31 13:00:50 | req_013HTizKBaHEi29X8QZHhA3u | claude-3-7-sonnet-20250219 |           | 9930         | 181           | Streaming |
| 2025-03-31 13:00:54 | req_01G4c4w5voe3nCVC3LBsayTh |                            |           | 1713         | 152           | Streaming |
| 2025-03-31 13:00:57 | req_01XkF4pwCxNkUGP2LAgKXY3F |                            |           | 1713         | 152           | Streaming |
| 2025-03-31 13:01:00 | req_01JMFyjcbHz5CGAa5Dpc7ACa |                            |           | 1713         | 152           | Streaming |
| 2025-03-31 13:01:03 | req_013N9okutyZMuiN8vHVq5fQr |                            |           | 1713         | 152           | Streaming |
| 2025-03-31 13:01:06 | req_01K8jpWbg3S5wYzPX7khofdJ |                            |           | 1713         | 152           | Streaming |
| 2025-03-31 13:01:09 | req_01WjLFXriL1taPemJfTQWUVH |                            |           | 1713         | 152           | Streaming |
| 2025-03-31 13:01:11 | req_01CnPCUMN4sE4in437nXZTd2 |                            |           | 1713         | 152           | Streaming |
| 2025-03-31 13:01:15 | req_01LBZsrjXjoXRn5PqTQFDSSu |                            |           | 1713         | 152           | Streaming |
| 2025-03-31 13:01:19 | req_012qqU8rNaYHDKn9VzyAidvf |                            |           | 1713         | 152           | Streaming |
| 2025-03-31 13:01:22 | req_01TG6w4N7Qrni5iBLXMo9vFi |                            |           | 1713         | 152           | Streaming |
| 2025-03-31 13:01:25 | req_01EwneR3SHVzS1sa3nyNJdX5 |                            |           | 1713         | 152           | Streaming |
| 2025-03-31 13:01:28 | req_012VxutBPoXuQkP6oorjRFeT |                            |           | 1713         | 152           | Streaming |
| 2025-03-31 13:01:33 | req_019nYtFdxqF3LJURGhuaSqDy |                            |           | 1713         | 152           | Streaming |
| 2025-03-31 13:01:36 | req_011NoVQsNjMo2zhGeRdsgQMM |                            |           | 1713         | 152           | Streaming |
| 2025-03-31 13:01:40 | req_01Kqj69r5WQpbBRRKq6Fse45 |                            |           | 1713         | 152           | Streaming |
| 2025-03-31 13:01:43 | req_0117Nuu8c7E11qHt7isZJ7Ug |                            |           | 1713         | 152           | Streaming |
| ...                 | ...                          | ...                        | ...       | ...          | ...           | ...       |
| 2025-03-31 14:41:11 | req_01VozVJGecmdMMrqzeBKBMG7 |                            |           | 1713         | 152           | Streaming |
| 2025-03-31 14:41:14 | req_01PxWkuoE7WKycVLWNBBScmp |                            |           | 1713         | 152           | Streaming |
| 2025-03-31 14:41:17 | req_01VQyKgUtMeobpokvZaavcvQ |                            |           | 1713         | 152           | Streaming |
| 2025-03-31 14:41:20 | req_01UJFGBtDeFhxX46MRHF8n4g |                            |           | 1713         | 152           | Streaming |
| 2025-03-31 14:41:24 | req_01YTjVBTjkUGWswjxRLcj2fY |                            |           | 1713         | 152           | Streaming |
| 2025-03-31 14:41:27 | req_01Uv9Z1taSdFyNUSCkQdfz79 |                            |           | 1713         | 152           | Streaming |
| 2025-03-31 14:41:30 | req_01DasvmHDmaJj3UGTH5ZLPeo |                            |           | 1713         | 152           | Streaming |
| 2025-03-31 14:41:33 | req_01L4nETqy6FkfT6XFwP1S7WZ |                            |           | -            | -             | Streaming |
| 2025-03-31 14:42:13 | req_014zsZQBxUjmGZmzoQ4ypLL1 |                            |           | 10167        | 66            | Streaming |
| 2025-03-31 14:42:22 | req_01KzmeoNLemLAUcxr2F9BkRK |                            |           | 10288        | 66            | Streaming |
| 2025-03-31 14:42:27 | req_01Gdg1ePkCH99tubaQqFTyrJ |                            |           | 10408        | 67            | Streaming |
| 2025-03-31 14:42:30 | req_01DutYzcjcKKCFEuZurp6U4q |                            |           | 21793        | 67            | Streaming |
| 2025-03-31 14:42:33 | req_01SBtuGWyavhzhEgapfiSrn6 |                            |           | 24981        | 71            | Streaming |
| 2025-03-31 14:42:36 | req_01VrMDRLNzrwkPt3QuqreAbJ |                            |           | 28175        | 74            | Streaming |
| 2025-03-31 14:42:59 | req_01F7UEWKMSNn5TS2yzVA8XVU |                            |           | 28376        | 349           | Streaming |
| 2025-03-31 14:43:26 | req_015wcEriaMQZRNZqQ6Q1bMF6 |                            |           | 28746        | 97            | Streaming |
| 2025-03-31 14:43:53 | req_01HBGvmjv5LZzW5gZiX3o775 |                            |           | 29278        | 235           | Streaming |
| 2025-03-31 14:44:20 | req_01XK95FCMeLChqqkeqCrRm6M |                            |           | 29534        | 235           | Streaming |
| 2025-03-31 14:45:25 | req_01LeixyxX4hCiDrZTVAsJe4j |                            |           | 29790        | 178           | Streaming |
| 2025-03-31 14:45:53 | req_012Y5TLhjKkE2n9h5jPB1YUF |                            |           | 29989        | 178           | Streaming |
| 2025-03-31 14:46:20 | req_01EQy5gSHZfEoDxomcgMtfS7 |                            |           | 30188        | 178           | Streaming |
| 2025-03-31 14:46:47 | req_0117XwPYWJWGdPFd8yD3PRYN |                            |           | 30387        | 178           | Streaming |
| 2025-03-31 14:47:17 | req_016TQesBjoXDn7jm5u41rNqL |                            |           | 30586        | 178           | Streaming |
| 2025-03-31 14:47:45 | req_01SduRw9pcppZ7c14WyGehZy |                            |           | 30785        | 178           | Streaming |
| 2025-03-31 14:48:12 | req_01AaNRZKkNkQ7gqTLAhtxKUD |                            |           | 30984        | 178           | Streaming |
| 2025-03-31 14:48:39 | req_01DUsi5n4X5EBRHsJrbGVmCi |                            |           | 31183        | 178           | Streaming |
| 2025-03-31 14:49:50 | req_01DWVL5ctwyxrRs3FNA88Ljq |                            |           | 31382        | 178           | Streaming |
| 2025-03-31 14:50:17 | req_01UsQRSuf69Dp2629tJRgCmt |                            |           | 31581        | 178           | Streaming |
| 2025-03-31 14:50:44 | req_01VjWqrdbPAokTqneQWkmBGw |                            |           | 31780        | 178           | Streaming |
| 2025-03-31 14:51:12 | req_01VyAsTqSuHHwuXMUsd9DsLC |                            |           | 31979        | 178           | Streaming |
| 2025-03-31 14:51:40 | req_01S1soVkjRvqV5HRCXMtcQHC |                            |           | 32178        | 178           | Streaming |
| 2025-03-31 14:52:07 | req_0197CBQ2vrgGDe1HWipiBXZ1 |                            |           | 32377        | 178           | Streaming |
| 2025-03-31 14:53:21 | req_01E5WajDHRtK37L78qT4gzfc |                            |           | 32576        | 178           | Streaming |
| 2025-03-31 14:53:48 | req_01HfRj6MmyBRnbgFYb7FUQwi |                            |           | 32775        | 178           | Streaming |
| 2025-03-31 14:54:15 | req_01GxuEpKTQ5T8RgVeGkayDKG |                            |           | 32974        | 178           | Streaming |
| 2025-03-31 14:54:42 | req_01B33pzGdABj2GnePfRBnfjm |                            |           | 33173        | 178           | Streaming |
| 2025-03-31 14:55:09 | req_019JeGN3Xy6xmsDKNt5eHoL2 |                            |           | 33372        | 178           | Streaming |
| 2025-03-31 14:56:25 | req_01EeGps3i1XvF4GVDhyqtBP6 |                            |           | 33571        | 178           | Streaming |
| 2025-03-31 14:56:52 | req_01TtQ1hs8JUJEF4N4edThNYY |                            |           | 33770        | 178           | Streaming |
| 2025-03-31 14:57:19 | req_01Eahy2JwPc6w57MERFbvPyh |                            |           | 33969        | 178           | Streaming |
| 2025-03-31 14:57:49 | req_01HCfchfWfLqoNqQX2iF9iMM |                            |           | 34168        | 178           | Streaming |
| 2025-03-31 14:58:18 | req_0187a1oVizcnGh15XC2KQGpc |                            |           | 34367        | 178           | Streaming |
| 2025-03-31 14:59:37 | req_01Hb9kW75AtdVwVcUFU6swGy |                            |           | 34566        | 178           | Streaming |
| 2025-03-31 15:00:05 | req_012bGkrDV5HQLazXFK7fA3Ct |                            |           | 34765        | 178           | Streaming |
| 2025-03-31 15:00:32 | req_01Q9SyqkcVhYFsP7K46hUnyM |                            |           | 34964        | 178           | Streaming |
| 2025-03-31 15:00:59 | req_01MHqTqSuF7Pox81edFhdjZ5 |                            |           | 35163        | 178           | Streaming |
| 2025-03-31 15:02:15 | req_017gduHJuLSrY7x8KmYUjdsM |                            |           | 35362        | 178           | Streaming |
| 2025-03-31 15:02:42 | req_01XnfamWhGcwXBt6UDsVaXAZ |                            |           | 35561        | 178           | Streaming |
| 2025-03-31 15:03:12 | req_01J6FSzaFp7KSFynA755ydYa |                            |           | 35760        | 178           | Streaming |
| 2025-03-31 15:03:40 | req_01VpgjZNe91iXcUGLZtXQxZ8 |                            |           | 35959        | 178           | Streaming |
| 2025-03-31 15:04:59 | req_01G8EeZVm7fUrKjBNRtKFp3E |                            |           | 36158        | 178           | Streaming |
| 2025-03-31 15:05:26 | req_014x337fE8jST8bHSuhqnKDj |                            |           | 36357        | 178           | Streaming |
| 2025-03-31 15:05:54 | req_01FaTAVZHXmf3K5Yj3fWmFPu |                            |           | 36556        | 178           | Streaming |
| 2025-03-31 15:06:21 | req_014j9c8A9pMt9JkAk22umsEL |                            |           | 36755        | 178           | Streaming |
| 2025-03-31 15:07:48 | req_015MQtjzDPtgYxXgXwz1CN1B |                            |           | 36954        | 178           | Streaming |
| 2025-03-31 15:08:15 | req_01UyNZAynX9ixcBqRo8maA5h |                            |           | 37153        | 178           | Streaming |
| 2025-03-31 15:08:42 | req_014uHnq4ZCtRWECKLMqd8bou |                            |           | 37352        | 178           | Streaming |
| 2025-03-31 15:09:09 | req_01Vvpv92qQstdqqupwzecNos |                            |           | 37551        | 178           | Streaming |
| 2025-03-31 15:10:41 | req_01P7Z1og5EAVQYEmd2yDndR5 |                            |           | 37750        | 178           | Streaming |
| 2025-03-31 15:11:09 | req_01EHcbqm5GPzW9C27fkZBvxS |                            |           | 37949        | 178           | Streaming |
| 2025-03-31 15:11:36 | req_01TSutdxg2YNaw4s35VfhZji |                            |           | 38148        | 178           | Streaming |
| 2025-03-31 15:12:03 | req_013kzRhSzS9aqTMcPJwGzJE7 |                            |           | 38347        | 178           | Streaming |
| 2025-03-31 15:13:41 | req_01H1R4HwBu78AameV3MFXELF |                            |           | 38546        | 178           | Streaming |
| 2025-03-31 15:13:53 | req_017CoQdfYpX7WzNn43q5gmGc |                            |           | 38745        | 178           | Streaming |
| 2025-03-31 15:14:20 | req_01HtkD9eKUqJiidFRFWWGAew |                            |           | 38944        | 178           | Streaming |
| 2025-03-31 15:15:55 | req_01Gb6RJP2Xpu34CNg219VZNY |                            |           | 39143        | 178           | Streaming |
| 2025-03-31 15:16:22 | req_01Ps7WZKHR7s3xjFp9UBzJ5k |                            |           | 39342        | 178           | Streaming |
| 2025-03-31 15:16:50 | req_01Jkkt8hGY4ymVKfWKRcUsWF |                            |           | 39541        | 178           | Streaming |
| 2025-03-31 15:18:15 | req_017iPoLExj78k8bsW3PtuYNa |                            |           | 39740        | 178           | Streaming |
| 2025-03-31 15:18:46 | req_01V5TBS1Tmikdrq7qm85kXqT |                            |           | 39939        | 178           | Streaming |
| 2025-03-31 15:19:13 | req_01PmhGPsYXZEZv5Ns8hMQjnp |                            |           | 40138        | 178           | Streaming |
| 2025-03-31 15:19:41 | req_014LGdGmRpjBhHxHnuEzYdXo |                            |           | 40337        | 178           | Streaming |
| 2025-03-31 15:21:25 | req_01DG5PGtmA699WosFcwvDMu3 |                            |           | 40536        | 178           | Streaming |
| 2025-03-31 15:21:53 | req_017VyJGd3SsMBk2PQedVi54V |                            |           | 40735        | 178           | Streaming |
| 2025-03-31 15:22:21 | req_01RJpjZD96RpQPetFZxtT3SR |                            |           | 40934        | 178           | Streaming |
| 2025-03-31 15:23:50 | req_01H5UnDmQeQYU32WY96xBoM7 |                            |           | 41133        | 178           | Streaming |
| 2025-03-31 15:24:17 | req_01Nm1LMSai5PG9xPiZoyQcYC |                            |           | 41332        | 178           | Streaming |
| 2025-03-31 15:24:45 | req_01F725iWzrSfbDG5LViaMWRK |                            |           | 41531        | 178           | Streaming |
| 2025-03-31 15:26:17 | req_01JfsWswj6BQqUMc1ndivakT |                            |           | 41730        | 178           | Streaming |
| 2025-03-31 15:26:45 | req_01ELBuXxz4dpbC2TTY7isAav |                            |           | 41929        | 178           | Streaming |
| 2025-03-31 15:27:12 | req_01VkQoJ27sAUBUFGriaGFEW1 |                            |           | 42128        | 178           | Streaming |
| 2025-03-31 15:28:47 | req_01LWe89JjHy1uwBzZBnjjWqb |                            |           | 42327        | 178           | Streaming |
| 2025-03-31 15:29:15 | req_01XqvGvJZh7S9uCA2FFE5xby |                            |           | 42526        | 178           | Streaming |
| 2025-03-31 15:29:43 | req_01HSmWBxobQbpRz2NyaEehDu |                            |           | 42725        | 178           | Streaming |
| 2025-03-31 15:31:20 | req_012LpPTgNyDxGXPh6TCWWNck |                            |           | 42924        | 178           | Streaming |
| 2025-03-31 15:31:47 | req_014vvwc8SUMgNqzwSCQCif9g |                            |           | 43123        | 178           | Streaming |
| 2025-03-31 15:32:14 | req_01Ra8ejYGjo3Eatp6SdtrJHN |                            |           | 43322        | 178           | Streaming |
| 2025-03-31 15:33:57 | req_01FSCUBwoKTUFHJjscTqySe1 |                            |           | 43521        | 178           | Streaming |
| 2025-03-31 15:34:24 | req_013zBMCQoZFiQKPLLc2g7DaZ |                            |           | 43720        | 178           | Streaming |
| 2025-03-31 15:34:51 | req_01NtHPtGHaQwLGYBnNEEQn1P |                            |           | 43919        | 178           | Streaming |
| 2025-03-31 15:36:35 | req_01G2U43UiQWUKwPwagXyMHjA |                            |           | 44118        | 178           | Streaming |
| 2025-03-31 15:37:02 | req_01PTshK8GjkEMdug92HaVpjj |                            |           | 44317        | 178           | Streaming |
| 2025-03-31 15:37:30 | req_01EPj46ZwRUNz7t5SsV6UVAG |                            |           | 44516        | 178           | Streaming |
| 2025-03-31 15:39:14 | req_01Khw1qFtix791YARH1jLy8z |                            |           | 44715        | 178           | Streaming |
| 2025-03-31 15:39:43 | req_018exfMQZLFEJgmqjg1K7Nrj |                            |           | 44914        | 178           | Streaming |
| 2025-03-31 15:40:10 | req_01AH44Tk7AuD72EyzZfucRvL |                            |           | 45113        | 178           | Streaming |
| 2025-03-31 15:41:57 | req_01VheCFoYwY2DNnzErXCNtYj |                            |           | 45312        | 178           | Streaming |
| 2025-03-31 15:42:24 | req_01UqBfX8QP7HpzLgSNH8UQWm |                            |           | 45511        | 178           | Streaming |
| 2025-03-31 15:42:51 | req_01YRwGdQCMBcQ65vMkxDZWVD |                            |           | 45710        | 178           | Streaming |
| 2025-03-31 15:44:47 | req_015bWtY7qLVazZuS6sPs26v2 |                            |           | 45909        | 178           | Streaming |
