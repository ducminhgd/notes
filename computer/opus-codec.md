---
type: codec
keywords:
- codec
- audio
- media
---
# Opus Codec

Opus is a totally open, royalty-free, highly versatile audio codec

## Technology

Opus can handle a wide range of audio applications, including Voice over IP, videoconferencing, in-game chat, and even remote live music performances. It can scale from low bitrate narrowband speech to very high quality stereo music. Supported features are:

- Bitrates from 6 kb/s to 510 kb/s
- Sampling rates from 8 kHz (narrowband) to 48 kHz (fullband)
- Frame sizes from 2.5 ms to 60 ms
- Support for both constant bitrate (CBR) and variable bitrate (VBR)
- Audio bandwidth from narrowband to fullband
- Support for speech and music
- Support for mono and stereo
- Support for up to 255 channels (multistream frames)
- Dynamically adjustable bitrate, audio bandwidth, and frame size
- Good loss robustness and packet loss concealment (PLC)
- Floating point and fixed-point implementation

Opus has the low algorithmic delay (26.5 ms by default) necessary for use as part of a real-time communication link, networked music performances, and live lip sync; by trading-off quality or bitrate, the delay can be reduced down to 5 ms