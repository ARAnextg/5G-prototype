Lab 2: Data Encoding and Decoding with BPSK signals and LDPC codes
=====================================================================

This lab explores the concepts of data encoding and decoding in the context of digital communication systems. We'll start with BPSK modulation, a fundamental digital modulation technique, and simulate the transmission of a modulated signal through a noisy channel using the Additive White Gaussian Noise (AWGN) model.



.. toctree::
   :maxdepth: 2
   :caption: Contents:

Introduction to BPSK Modulation
-------------------------------

Binary Phase Shift Keying (BPSK) is one of the simplest forms of phase modulation technique that uses two phases to represent binary numbers. In this part, we will generate a random binary message, modulate it using BPSK, and visualize the modulated signal.

**Procedure**:

1. **Generate a Binary Message**: Start by generating a random binary sequence. This will act as our original message for modulation.

2. **Apply BPSK Modulation**: Modulate the binary message using BPSK. In BPSK, we map binary `0` to one phase and binary `1` to another phase.

3. **Visualize the Signal**: Plot the modulated signal to visualize how the binary message is represented in the BPSK signal.

**MATLAB Code**:

.. code-block:: matlab

  % Example for generating a DVB-S2 LDPC parity-check matrix
  codeRate = 1/2;
  ldpcMatrix = dvbs2ldpc(codeRate);

  % Adjust the binary message length to match the number of information bits for the code
  binaryMessage = randi([0 1], size(ldpcMatrix, 2) - size(ldpcMatrix, 1), 1);

   % BPSK Modulation
   bpskModulated = 2 * binaryMessage - 1;

   % Plot the BPSK modulated signal
   figure;
   stem(bpskModulated, 'filled');
   title('BPSK Modulated Signal');
   xlabel('Bit Index');
   ylabel('Modulated Signal Value');
   grid on;

.. figure:: /images/modulated_bpsk.bmp
   :align: center
   :alt: BPSK Modulation Process

   The process of BPSK modulation of a binary message.

The BPSK Modulation Process figure shows the modulated signal resulting from applying Binary Phase Shift Keying (BPSK) to a random binary message. Each bit of the original message is represented as either a high or low amplitude in the signal, corresponding to binary `1` and `0`, respectively. This visualization demonstrates how digital data is prepared for transmission over a communication channel by converting bits into a form suitable for the physical layer.


Simulating a Noisy Channel
--------------------------

After modulating the binary message, the next step in our communication system simulation is to transmit the signal through a noisy channel. The Additive White Gaussian Noise (AWGN) model is commonly used to simulate the effect of noise in wireless communication systems.

**Procedure**:

1. **Define the Signal-to-Noise Ratio (SNR)**: Choose an SNR value to determine the level of noise added to the signal. A lower SNR value means more noise.

2. **Transmit Through AWGN Channel**: Use MATLAB to add AWGN to the BPSK modulated signal, simulating transmission through a noisy channel.

3. **Visualize the Noisy Signal**: Plot the noisy signal to observe the effect of noise on the BPSK signal.

**MATLAB Code**:

.. code-block:: matlab

   % Define SNR
   snr = 10; % Signal to Noise Ratio in dB

   % Add AWGN to the BPSK modulated signal
   awgnChannel = comm.AWGNChannel('NoiseMethod', 'Signal to noise ratio (SNR)', 'SNR', snr);
   noisySignal = step(awgnChannel, bpskModulated.');

   % Plot the noisy BPSK signal
   figure;
   plot(real(noisySignal), 'b.');
   title('Noisy BPSK Signal');
   xlabel('Bit Index');
   ylabel('Signal Value');
   grid on;

.. figure:: /images/noisy_bpsk.bmp
   :align: center
   :alt: Noisy BPSK Signal

   Visualization of the BPSK signal after passing through an AWGN channel.
The Noisy BPSK Signal figure illustrates the effect of Additive White Gaussian Noise (AWGN) on the BPSK modulated signal during transmission through a noisy channel. The plot highlights how the originally distinct signal values become less discernible due to the presence of noise, simulating real-world conditions where communication signals are subject to interference and distortion. This scenario underscores the importance of robust modulation and coding techniques in maintaining data integrity in noisy environments. 


LDPC Encoding
-------------

Low-Density Parity-Check (LDPC) codes are an error-correcting code that provides a method for transmitting messages over noisy transmission channels more reliably. LDPC codes are particularly effective in close-to-capacity conditions. In this part, we will encode our binary message using an LDPC encoder to improve its robustness against channel noise.

**Procedure**:

1. **Initialize LDPC Encoder**: Use MATLAB's built-in functions to create an LDPC encoder object.

2. **Encode the Message**: Apply the encoder to the binary message, introducing redundancy that helps in error correction.

3. **Visualize the Encoded Message**: Although the encoded message will be longer than the original due to added parity bits, it's essential to understand the encoding process's impact on the message size.

**MATLAB Code**:

.. code-block:: matlab

   % Create an LDPC encoder object
   ldpcEncoder = comm.LDPCEncoder('ParityCheckMatrix', ldpcMatrix);

   % Encode the binary message
   encodedMessage = step(ldpcEncoder, binaryMessage);

.. note:: After encoding with LDPC, the message length increases due to the addition of parity bits. These bits are essential for detecting and correcting errors in the receiver side, showcasing the trade-off between redundancy and error resilience.

BPSK Decoding
-------------

After transmission through a noisy channel, the received signal must be decoded back into a binary message. This process involves demodulating the BPSK signal and then applying error correction codes if necessary.

**Procedure**:

1. **BPSK Demodulation**: Convert the received noisy signal back into binary format using a thresholding technique.

2. **Visualize the Demodulated Signal**: Plot the demodulated signal to compare it with the original binary message.

**MATLAB Code**:

.. code-block:: matlab

   % BPSK Demodulation
   demodulatedSignal = noisySignal > 0;  % Convert to 0 and 1 based on sign

   % Visualize the demodulated signal
   figure;
   stem(demodulatedSignal, 'filled');
   title('Demodulated BPSK Signal');
   xlabel('Bit Index');
   ylabel('Signal Value');
   grid on;

.. figure:: /images/demodulated_bpsk.bmp
   :align: center
   :alt: Demodulated BPSK Signal

This figure illustrates the demodulated BPSK signal, showcasing the process of converting the received noisy signal back into a binary format. Despite the presence of noise, the fundamental structure of the original message can still be discerned, highlighting the effectiveness of BPSK modulation and demodulation techniques.

LDPC Decoding
-------------

The final step in the communication process involves decoding the message using an LDPC decoder. This step corrects any errors introduced by the channel noise, ensuring the integrity of the transmitted message.

**Procedure**:

1. **Initialize LDPC Decoder**: Create an LDPC decoder object in MATLAB.

2. **Decode the Message**: Apply the decoder to the demodulated signal to correct errors.

3. **Compare Original and Decoded Messages**: Analyze the effectiveness of the LDPC coding by comparing the original message with the decoded one.

**MATLAB Code**:

.. code-block:: matlab

   % LDPC Decoding
   ldpcDecoder = comm.LDPCDecoder('ParityCheckMatrix', ldpcMatrix);
   decodedMessage = step(ldpcDecoder, double(demodulatedSignal));

   % Calculate the number of bit errors
   bitErrors = sum(abs(decodedMessage - binaryMessage.'));
   fprintf('Number of bit errors: %d\\n', bitErrors);
   % Calculate the Bit Error Rate (BER)
   BER = bitErrors / length(binaryMessage);
   fprintf('Bit Error Rate (BER) after decoding: %f\n', BER);

.. note:: The effectiveness of LDPC codes in correcting errors is evident when comparing the number of bit errors before and after decoding. A significant reduction in errors demonstrates the robustness of LDPC codes against channel noise. The example LDPC codes that we've utilized in this lab is just an example and may result in a much higher BER than what you would typically see with actual hardware.

