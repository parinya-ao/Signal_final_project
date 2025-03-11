# Secret Voice Communication System
# ระบบการส่งเสียงที่เป็นความลับ

![Python](https://img.shields.io/badge/python-3.12.3-blue.svg)
![License](https://img.shields.io/badge/license-MIT-green.svg)

## Overview | ภาพรวม

This project presents a secure voice communication system that utilizes frequency shifting techniques to transfer speech signals to frequency ranges that humans can hear but cannot comprehend (10-18 kHz). This creates a privacy layer for sensitive communications without requiring encryption.

โปรเจคนี้นำเสนอระบบการสื่อสารเสียงที่มีความปลอดภัยโดยใช้เทคนิคการเลื่อนความถี่สัญญาณไปยังย่านที่มนุษย์ได้ยินแต่จับใจความไม่ได้ (10-18 kHz) ทำให้เกิดการสื่อสารที่เป็นส่วนตัวได้โดยไม่จำเป็นต้องเข้ารหัส

## Features | คุณสมบัติ

- **Frequency Shifting**: Uses Hilbert Transform to shift voice frequency to 10-18 kHz range
- **Dual Channel Support**: Can transmit two audio signals simultaneously on different frequency bands
- **Noise Reduction**: Implements low-pass filtering to reduce background noise
- **High Quality Recovery**: Restored audio maintains excellent quality metrics
- **Real-time Processing**: Can process audio signals in real time

- **การเลื่อนความถี่**: ใช้การแปลง Hilbert เพื่อเลื่อนความถี่เสียงไปยังช่วง 10-18 kHz
- **รองรับ 2 ช่องสัญญาณ**: สามารถส่งสัญญาณเสียง 2 ช่องพร้อมกันในย่านความถี่ที่แตกต่างกัน
- **การลดสัญญาณรบกวน**: ใช้ตัวกรองความถี่ต่ำเพื่อลดเสียงรบกวนพื้นหลัง
- **การกู้คืนคุณภาพสูง**: เสียงที่กู้คืนมีค่าเมทริกซ์คุณภาพที่ยอดเยี่ยม
- **การประมวลผลแบบเรียลไทม์**: สามารถประมวลผลสัญญาณเสียงแบบเรียลไทม์ได้

## Requirements | ความต้องการระบบ

- Python 3.12.3
- NumPy
- SciPy
- Matplotlib
- Soundfile
- Sounddevice
- Librosa

## Installation | การติดตั้ง

```bash
# Clone the repository
git clone https://github.com/parinya-ao/secret-voice-communication.git
cd secret-voice-communication

# Create virtual environment (optional)
python -m venv venv
source venv/bin/activate  # On Windows: venv\Scripts\activate

# Install dependencies
pip install numpy scipy matplotlib soundfile sounddevice librosa
```

## System Architecture | สถาปัตยกรรมระบบ

The system consists of two main components:

1. **Transmitter**:
   - Receives microphone input
   - Reduces background noise using low-pass filtering
   - Shifts frequency to the target range (10-18 kHz)
   - Outputs through speakers

2. **Receiver**:
   - Captures the shifted audio
   - Applies band-pass filtering to isolate the target frequency range
   - Shifts the frequency back to the original range
   - Outputs the recovered audio

ระบบประกอบด้วย 2 ส่วนหลัก:

1. **ภาคส่ง (Transmitter)**:
   - รับสัญญาณจากไมโครโฟน
   - ลดสัญญาณรบกวนพื้นหลังด้วยตัวกรองความถี่ต่ำ (Low-pass filter)
   - เลื่อนความถี่ไปยังช่วงเป้าหมาย (10-18 kHz)
   - ส่งสัญญาณผ่านลำโพง

2. **ภาครับ (Receiver)**:
   - รับสัญญาณเสียงที่ถูกเลื่อนความถี่
   - ใช้ตัวกรองความถี่ผ่าน (Band-pass filter) เพื่อแยกช่วงความถี่เป้าหมาย
   - เลื่อนความถี่กลับมายังช่วงเดิม
   - ส่งสัญญาณเสียงที่กู้คืนมาได้

## Usage | การใช้งาน

### Basic Usage | การใช้งานพื้นฐาน

```python
# Import required libraries
import numpy as np
from scipy.io import wavfile
from scipy.signal import hilbert
import soundfile as sf

# Load audio file
input_path = 'input_audio.wav'
fs, audio = wavfile.read(input_path)

# Define frequency shifting function
def shift_frequency(audio, fs, shift_amount):
    audio = audio.astype(np.float32)
    analytic_signal = hilbert(audio)
    t = np.arange(len(audio)) / fs
    shifted_signal_complex = analytic_signal * np.exp(1j * 2 * np.pi * shift_amount * t)
    shifted_signal = np.real(shifted_signal_complex)
    return shifted_signal

# Shift frequency (e.g., by 10kHz)
shifted_audio = shift_frequency(audio, fs, 10000)

# Save shifted audio
output_path = 'shifted_audio.wav'
sf.write(output_path, shifted_audio.astype(np.int16), fs)
```

### Complete Pipeline | กระบวนการทั้งหมด

See `main.py` for a complete implementation of the audio processing pipeline, including:
- Noise reduction
- Frequency shifting
- Audio combination
- Filtering
- Frequency shifting back to original range
- Quality evaluation

ดูไฟล์ `main.py` สำหรับการใช้งานกระบวนการประมวลผลเสียงทั้งหมด ซึ่งรวมถึง:
- การลดสัญญาณรบกวน
- การเลื่อนความถี่
- การรวมสัญญาณเสียง
- การกรองสัญญาณ
- การเลื่อนความถี่กลับไปยังช่วงเดิม
- การประเมินคุณภาพ

## Technical Details | รายละเอียดทางเทคนิค

### Frequency Shifting | การเลื่อนความถี่

The frequency shifting is implemented using the Hilbert transform to create an analytic signal, which is then multiplied by a complex exponential:

การเลื่อนความถี่ใช้การแปลง Hilbert เพื่อสร้างสัญญาณวิเคราะห์ (analytic signal) จากนั้นคูณด้วยฟังก์ชัน complex exponential:

```
x_analytic(t) = x(t) + j * hilbert(x(t))
x_shifted(t) = Re[x_analytic(t) * e^(j*2π*f_shift*t)]
```

### Noise Reduction | การลดสัญญาณรบกวน

Low-pass filtering is used to reduce background noise, focusing on the 0-3000 Hz range which contains most human speech:

การกรองความถี่ต่ำถูกใช้เพื่อลดสัญญาณรบกวนพื้นหลัง โดยเน้นช่วงความถี่ 0-3000 Hz ซึ่งเป็นช่วงที่มีเสียงพูดของมนุษย์ส่วนใหญ่:

```python
# Apply low-pass filter
def low_pass_filter(audio, fs, cutoff=3000):
    audio_fft = fft(audio)
    frequencies = fftfreq(len(audio), 1/fs)
    mask = (np.abs(frequencies) <= cutoff)
    filtered_fft = audio_fft * mask
    filtered_audio = ifft(filtered_fft).real
    return filtered_audio
```

## Evaluation Results | ผลการประเมิน

The system achieves excellent quality metrics for recovered audio:

ระบบสามารถกู้คืนเสียงได้ด้วยคุณภาพที่ดีเยี่ยม โดยมีค่าเมทริกซ์ดังนี้:

| Metric | Signal 1 | Signal 2 |
|--------|----------|----------|
| SNR (dB) | 17.48 | 16.09 |
| PSNR (dB) | 37.38 | 39.79 |
| Cross-correlation | 0.9914 | 0.9876 |
| Spectral Distortion | 0.0000 | 0.0000 |
| PESQ | 3.98 | 4.11 |

## Applications | การนำไปใช้งาน

- Military communications
- Confidential meetings
- Private communications in public spaces
- Security systems

- การสื่อสารทางทหาร
- การประชุมที่เป็นความลับ
- การสื่อสารส่วนตัวในพื้นที่สาธารณะ
- ระบบรักษาความปลอดภัย

## Limitations | ข้อจำกัด

- The system is designed for short-range communications
- Performance degrades in high-noise environments
- Not a cryptographic solution - provides privacy through obscurity
- Requires specialized equipment to decode the shifted signal

- ระบบถูกออกแบบสำหรับการสื่อสารระยะใกล้
- ประสิทธิภาพลดลงในสภาพแวดล้อมที่มีเสียงรบกวนสูง
- ไม่ใช่การเข้ารหัสทางการ - ให้ความเป็นส่วนตัวผ่านการซ่อนข้อมูล
- ต้องใช้อุปกรณ์เฉพาะในการถอดรหัสสัญญาณที่ถูกเลื่อนความถี่

## Future Work | งานในอนาคต

- Implementing real-time processing on embedded systems
- Adding encryption layer for higher security
- Optimizing for longer transmission distances
- Developing mobile applications for everyday use

- การพัฒนาการประมวลผลแบบเรียลไทม์บนระบบฝังตัว
- เพิ่มชั้นการเข้ารหัสเพื่อความปลอดภัยที่สูงขึ้น
- การปรับปรุงประสิทธิภาพสำหรับการส่งสัญญาณระยะไกล
- พัฒนาแอปพลิเคชันมือถือสำหรับการใช้งานในชีวิตประจำวัน

## Author | ผู้จัดทำ

- Parinya Aobaun | ปริญญา อบอุ่น
  - Department of Computer Engineering, Faculty of Engineering, Kasetsart University
  - ภาควิชาวิศวกรรมคอมพิวเตอร์ คณะวิศวกรรมศาสตร์ มหาวิทยาลัยเกษตรศาสตร์

## Acknowledgments | กิตติกรรมประกาศ

- Dr. Kittipol Horapong for project guidance and supervision
- Saksorn Buaphuek for technical support and ideas

- อาจารย์ ดร.กิตติพล โหราพงศ์ สำหรับการให้คำปรึกษาและการควบคุมดูแลโครงการ
- นายศักย์ศรณ์ บัวเผือก สำหรับการสนับสนุนทางเทคนิคและข้อคิด

## License | ลิขสิทธิ์

This project is licensed under the MIT License - see the LICENSE file for details.

โครงการนี้เผยแพร่ภายใต้สัญญาอนุญาต MIT - ดูรายละเอียดเพิ่มเติมในไฟล์ LICENSE
