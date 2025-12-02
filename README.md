# Frequency-Division-Multiplexing---Modulation-and-Demodulation-using-Python

__Aim__:

To generate an FDM signal by multiplexing multiple baseband message signals on different carrier frequencies, transmit (sum) them, optionally add channel noise, then recover each message by bandpass filtering and coherent demodulation in Python (Google Colab). Observe time & frequency domain signals and measure recovery quality.


__Apparatus Required__:

Google Colab (or any Python environment)

Python libraries: numpy, matplotlib, scipy (scipy.signal)


__Theory__:

FDM places different message signals in separate, non-overlapping frequency bands by modulating each message onto a distinct carrier frequency. The multiplexed signal is the sum of all modulated channels. At the receiver, bandpass filters (or tuned filters) isolate each channel; then each isolated carrier is demodulated (coherently multiplied by a synchronized carrier) and low-pass filtered to recover the original baseband.

__Procedure__:

1 — Imports and parameters

2 — Create message signals and carriers

3 — Modulate each message (standard AM DSB-SC) and form FDM signal

4 — Frequency domain (spectrum) of FDM signal

5 — (Optional) Add AWGN noise to FDM signal

6 — Receiver: isolate each channel with bandpass filter

7 — Demodulate each isolated channel (coherent) and low-pass filter to recover baseband

PROGRAM:
~~~
import numpy as np
import matplotlib.pyplot as plt
from scipy import signal

# -------------------------------------------------------------
# Parameters
# -------------------------------------------------------------
fs = 50000                  # Sampling frequency (50 kHz)
T = 0.01                    # Total duration (10 ms)
t = np.arange(0, T, 1/fs)   # Time vector

N = 5                       # Number of message channels

# Message signal frequencies
msg_freq = np.array([120, 240, 340, 500, 800])

# Carrier frequencies (must be spaced apart)
carrier_freq = np.array([3000, 6000, 9000, 12000, 15000])

# -------------------------------------------------------------
# Create messages (each row is one signal)
# -------------------------------------------------------------
messages = np.sin(2*np.pi*msg_freq[:, None] * t)

# Carriers for modulation
carriers = np.cos(2*np.pi*carrier_freq[:, None] * t)

# -------------------------------------------------------------
# Modulation (AM / Synchronous)
# -------------------------------------------------------------
modulated = messages * carriers

# Composite FDM waveform
fdm_signal = np.sum(modulated, axis=0)

# -------------------------------------------------------------
# Demodulation (Multiply by carrier + LPF)
# -------------------------------------------------------------
demodulated_raw = 2 * (fdm_signal[None, :] * carriers)

# Low-pass filter to recover baseband
cutoff_hz = 1200.0  # Must be > highest message freq (800 Hz)
b, a = signal.butter(6, cutoff_hz / (0.5 * fs), btype='low')

demodulated = signal.filtfilt(b, a, demodulated_raw, axis=1)

# -------------------------------------------------------------
# Plot original message signals
# -------------------------------------------------------------
plt.rcParams.update({'figure.max_open_warning': 0})
fig1, axes1 = plt.subplots(N, 1, figsize=(8, 8), sharex=True)
fig1.suptitle("Original Message Signals")

for i, ax in enumerate(axes1):
    ax.plot(t, messages[i, :])
    ax.set_ylabel(f"m{i+1}")

axes1[-1].set_xlabel("Time (s)")
plt.tight_layout(rect=[0, 0, 1, 0.96])

# -------------------------------------------------------------
# Plot composite FDM signal
# -------------------------------------------------------------
fig2, ax2 = plt.subplots(1, 1, figsize=(10, 3))
ax2.plot(t, fdm_signal)
ax2.set_title("Composite FDM Signal")
ax2.set_xlabel("Time (s)")
ax2.set_ylabel("Amplitude")
plt.tight_layout()

# -------------------------------------------------------------
# Plot recovered (demodulated) signals
# -------------------------------------------------------------
fig3, axes3 = plt.subplots(N, 1, figsize=(8, 8), sharex=True)
fig3.suptitle("Demodulated (Recovered) Signals after LPF")

for i, ax in enumerate(axes3):
    ax.plot(t, demodulated[i, :])
    ax.set_ylabel(f"rec{i+1}")

axes3[-1].set_xlabel("Time (s)")
plt.tight_layout(rect=[0, 0, 1, 0.96])

plt.show()

# -------------------------------------------------------------
# Comparison: Original vs Recovered (Zoomed)
# -------------------------------------------------------------
ch = 0  # Compare channel 1
end_sample = int(0.002 * fs)   # First 2 ms

plt.figure(figsize=(8,3))
plt.plot(t[:end_sample], messages[ch, :end_sample], label='original m1')
plt.plot(t[:end_sample], demodulated[ch, :end_sample], '--', label='recovered m1')
plt.legend()
plt.xlabel("Time (s)")
plt.title("Original vs Recovered Signal (Channel 1) — Zoom")
plt.show()
~~~
__Output_:
![WhatsApp Image 2025-11-25 at 11 25 49_2537c7f7](https://github.com/user-attachments/assets/a2d0bc15-1298-478b-84e2-24e2542deed8)
![WhatsApp Image 2025-11-25 at 11 26 34_4b596cad](https://github.com/user-attachments/assets/62e4748c-8ef8-496d-a702-feb535d961b2)
![WhatsApp Image 2025-11-25 at 11 27 42_94580974](https://github.com/user-attachments/assets/a1bffb29-692f-4157-84d6-1207acedf058)


__Result__:

Thus frequency division multiplexing modulation and demodulation using python is done and verified

