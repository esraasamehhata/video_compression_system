# Video Compression System — CIE 347

A MATLAB implementation of a block-based video encoder/decoder covering the full pipeline: DCT transform coding, quantization, motion estimation and compensation, and entropy coding (Huffman + Arithmetic).

---

## Requirements

- MATLAB R2019b+
- Signal Processing Toolbox
- Communications Toolbox

---

## How to Run

```matlab
run('video_compression_system.m')
```

All experiments run automatically and generate figures. No external video needed — synthetic sequences are built in.

---

## System Pipeline

```
Input → Block partition → [I-frame: DCT → Quantize]
                          [P-frame: Motion Estimation → Residual → DCT → Quantize]
                        → Entropy Coding → Bitstream → Decoder → Reconstructed Video
```

---

## Components

| Component | Details |
|---|---|
| Transform coding | 2D DCT applied to intra blocks and inter residuals (`dct2`/`idct2`) |
| Quantization | Uniform scalar quantizer with configurable step Q |
| Motion estimation | Full-search SAD; configurable block and search range — **no overlapping blocks** (stride = block size, edge blocks zero-padded) |
| Entropy coding | **Huffman** — actual encoded bits via `huffmandict`/`huffmanenco`; **Arithmetic** — real fixed-point 32-bit coder with E1/E2/E3 renormalization (static frequency model) |
| Metrics | MSE, PSNR (MAX=255), Compression Ratio — exact spec formulas |

---

## Entropy Coding Notes

Both coders are genuine implementations, not approximations:

- **Huffman**: uses MATLAB's built-in coder; returns actual bit count. Single-symbol edge case (all-zero residual after perfect motion compensation) handled with a 1-bit flag.
- **Arithmetic**: fixed-point 32-bit integer coder with E1/E2/E3 renormalization. Frequency table scaled to 2¹⁶. Same single-symbol fallback as Huffman for a fair comparison. Uses a static (not adaptive) frequency model.

Arithmetic coding consistently produces 1–5% fewer bits than Huffman at the same quantization step, approaching the Shannon entropy bound more closely.

---

## Block Processing

Blocks are strictly **non-overlapping**: the encoder/decoder loop uses `stride = block_size` in both dimensions. Edge blocks that fall outside the frame boundary are zero-padded during encoding and only valid pixels are written back during decoding.

---

## Experiments

| # | Variable | Values |
|---|---|---|
| 1 | Quantization step Q | 2, 5, 10, 20, 50 |
| 2a | DCT block size | 4, 8, 16, 32 |
| 2b | Macroblock size (ME only, DCT fixed 8×8) | 4, 8, 16, 32 |
| 3 | Entropy coding method | Huffman vs Arithmetic (noise sequence) |
| 4 | Motion search range | 2, 4, 8, 16 |

Each experiment outputs a PSNR vs compression ratio curve and a console table.

---

## Known Limitations

- No B-frames (I and P only)
- Static frequency model in arithmetic coder (not adaptive)
- No zigzag + run-length encoding of DCT coefficients
- No perceptual quantization matrix
- No sub-pixel motion estimation
- Grayscale only

---

## Authors

- [Name 1]
- [Name 2]

Course: Information Theory and Coding — CIE 347
