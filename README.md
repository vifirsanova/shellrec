# ShellRec

Minimal screen recorder for Arch Linux with X11 and Wayland support. Uses FFmpeg (X11) or wf-recorder (Wayland) with optimized presets.

- Recording starts immediately after setup
- Stop with Ctrl+C
- Automatic file saving

## Features
- Auto-detects X11/Wayland
- System audio recording
- 5 FFmpeg presets: web, compressed, nvidia, universal, lossless
- Keystroke display (screenkey)
- Record fullscreen, window, or selected area
- Auto-dependency setup on first run

## Installation

```bash
git clone https://github.com/vifirsanova/shellrec.git
cd shellrec
chmod +x shellrec
sudo cp shellrec /usr/local/bin/
```

On first launch, ShellRec will check and install missing dependencies:
```bash
shellrec
```

## Usage

### Basic Recording
```bash
# Fullscreen recording (auto-detects X11/Wayland)
shellrec

# Show keystrokes
shellrec -key

# Select area with mouse
shellrec -area

# Record active window (X11 only)
shellrec -window
```

### FFmpeg Presets
```bash
# Web/YouTube optimized (libx264, balanced)
shellrec -set web

# Compressed for sharing (fast encoding)
shellrec -set compressed -out bug_report

# NVidia hardware encoding (h264_nvenc)
shellrec -set nvidia -fps 144 -out gameplay

# Universal compatibility
shellrec -set uni -key

# Lossless quality for editing (large files)
shellrec -set lossless
```

### All Options
```
-set PRESET     Preset: web, compressed, nvidia, uni, lossless
                Default: web
-key            Show keystrokes (screenkey)
                Default: off
-out NAME       Output filename (without extension)
                Default: YYYYMMDD_HHMMSS_recording
-res WxH        Resolution (1920x1080, 1280x720)
-fps N          Frames per second (default: 30)
-area           Select area with mouse
-window         Record active window (X11 only)
-fullscreen     Record entire screen (default)
-dir PATH       Output directory
-deps           Check/install dependencies
-h, --help      Show help
-v, --version   Show version
```

### Preset Details
- **web** - libx264, fast preset, CRF 28 (YouTube/streaming)
- **compressed** - libx264, ultrafast, CRF 32 (quick sharing)
- **nvidia** - h264_nvenc, p4 preset (hardware encoding)
- **uni** - libx264, medium, CRF 23 (universal compatibility)
- **lossless** - libx264, veryslow, CRF 0 (editing/archival)

## Display Server Support

### X11
- Full support via FFmpeg x11grab
- Audio via PulseAudio
- Window selection (xdotool)
- Area selection (slop)

### Wayland
- Basic support via wf-recorder
- Audio via wf-recorder
- Area selection (slurp)
- Window selection not supported

## Audio Recording
- **X11**: PulseAudio via FFmpeg
- **Wayland**: Via wf-recorder (auto-detected)

## Testing
```bash
# Quick 5-second test
shellrec -set compressed -fps 10 -out test &
sleep 5
kill %1
```

## Technical Details

### Backends
- **X11**: FFmpeg with `x11grab` for capture and `pulse` for audio
- **Wayland**: wf-recorder for capture and audio

Example FFmpeg command for 'web' preset:
```bash
ffmpeg -f x11grab -video_size 1920x1080 -framerate 30 \
       -i :0.0 -f pulse -i default \
       -c:v libx264 -preset fast -crf 28 \
       -c:a aac -movflags +faststart output.mp4
```

### Dependencies
Automatically installed on first run:

#### X11:
- ffmpeg (core backend)
- slop (area selection)
- xdotool (window capture)
- screenkey (keystroke display)

#### Wayland:
- wf-recorder (core backend)
- slurp (area selection)
- screenkey (keystroke display)

## Important Notes

### Limitations
- Command-line only
- Primarily tested on Arch Linux
- Recording starts immediately after setup
- Stop recording with Ctrl+C
- Wayland: no window selection
- `-window` option only works in X11
- `timeout` command not directly supported (use shell workaround)
- `-set uni` preset might have issues in some configurations

### File Locations
- Default: `~/Videos/shellrec/`
- Format: `YYYYMMDD_HHMMSS_recording.mp4` (format varies by preset)

## Troubleshooting

### Screen Recording Permission
```bash
xhost +local:
```

### NVidia Encoding Issues
```bash
sudo pacman -S nvidia nvidia-utils
nvidia-smi  # Verify driver installation
```

### FFmpeg Problems
```bash
# Check FFmpeg installation
ffmpeg -version

# Test screen capture manually
ffmpeg -f x11grab -video_size 1920x1080 -framerate 30 -i :0.0 -t 5 test.mp4
```

### No Audio Recording
```bash
# X11
pactl info  # Check PulseAudio

# Wayland
wf-recorder --audio  # Check audio support
```

### Window Recording Issues (X11)
```bash
# Test xdotool
xdotool getactivewindow
# If empty, window recording may not work
```

### Universal Preset Issues
If `-set uni` fails, try:
```bash
# Alternative presets
shellrec -set web
shellrec -set compressed
```

### View Logs
```bash
cat /tmp/shellrec.log
```

## Feedback
Created by [@vifirsanova](https://github.com/vifirsanova) (missvector)

Issues and feedback welcome on GitHub.

**Version:** 2.1 (X11 and Wayland support)
