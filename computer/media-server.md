---
type: media
keywords:
- media
- video
- streaming
- server
- service
---
# Media server

## Comparision

This table is created on 2022-09-21

|                        |            AntMedia             |                    Wowza                     |                                                              Nimble                                                               |       Jitsi        |                              Janus                               |                                              Flussonic                                               |
| ---------------------- | ------------------------------- | -------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------- | ------------------ | ---------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------- |
| **Hosting**            | Self-hosted, Cloud              | Self-hosted, Cloud                           | Self-hosted                                                                                                                       | Self-hosted, Cloud | Self-hosted                                                      | Self-hosted, Cloud                                                                                   |
| **Protocol**           | HLS, Dash, RTMP, WebRTC, SRT    | WebRTC, HLS, SRT                             | WebRTC (ingest) WHIP, RTMP/RTMPS, SRT, RIST, RTSP, MPEG-TS, HLS, DASH                                                             | WebRTC             | WebRTC                                                           | HLS, DASH,Low Latency,Player, WebRTC,MPEGTS, CBR,Cluster                                             |
| **Codecs**             | H.264, VP8 (Paid), H.265 (Paid) | H.264(All), H.263, H.265                     | Input: H.264, H.265, VP8, VP9, MPEG2, MPEG4. Output: H.264, H.265                                                                 | H.264              |                                                                  | H264, H265, MPEG-2 Video, MPEG-2 Audio, AAC, AC3, MP3, G711a                                         |
| **Recording**          | MP4                             | M4fv files (needs )                          | MP4                                                                                                                               |                    | `mjr` file                                                       | Yes, local disk or s3                                                                                |
| **Integration**        | SDK, Rest API                   | JS SDK, Rest API                             | API                                                                                                                               | SDK, API           | RESTful, WebSockets, RabbitMQ, MQTT, Nanomsg and UnixSockets API | API                                                                                                  |
| **Hardware trancoder** | Yes                             | Yes                                          | Yes                                                                                                                               |                    |                                                                  | Yes                                                                                                  |
| **Livestream DVR**     | Yes                             | Yes                                          | Yes (not support DASH)                                                                                                            |                    |                                                                  | Yes                                                                                                  |
| **Secure content**     | Token                           | BuyDRM, DRMtoday, EzDRM, Verimatrix VCAS DRM | Google Widevine, Microsoft Playready, Apple FairPlay, Widevine Cloud Service, BuyDRM KeyOS, EZDRM, PallyCon, Verimatrix VCAS CPIX |                    |                                                                  | EZDRM, DRM Conax, DRM Conax for Nagra, BuyDRM (KeyOS), Widevine, PallyCon, Irdeto, PlayReady, GS DRM |
| **Low latency**        | Yes                             | Yes                                          | Yes                                                                                                                               |                    |                                                                  | Yes                                                                                                  |
| **Free plan**          | Yes                             | No                                           | Yes                                                                                                                               | Yes                | Yes                                                              | No                                                                                                   |
| **Pricing**            | $69/instance/month              | $195/instance/month                          | $70/instance for livestreaming only                                                                                               | No                 | No                                                               | $249/instance/month                                                                                  |

## Read more

1. https://www.wowza.com/docs/how-to-extract-mp4-formatted-files-from-dvr-streams-using-wowza-streaming-engine-java-api
2. https://wmspanel.com/nimble/live_streaming