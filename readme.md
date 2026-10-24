# Canon FIR file format and crypto algorithms

## Introduction

Canon updates their DSLR or mirrorless cameras using update files in FIR format.

Version 4 of the format, starting 40D (2007) and before EOS R (2018) is using symmetric cryptography (hmac-sha1) to sign content, and previous work has been done as early as 2011:
- https://magiclantern.fandom.com/wiki/Firmware_file
- https://magiclantern.fandom.com/wiki/Fir_Security
- https://magiclantern.fandom.com/wiki/Update_records

This repository releases crypto algorithms about version 4 or the algorithms, whereas version 5 has been explained during Hack.lu 2025 conference. File formats for v4 and v5 are very similar, except for signatures and encryption headers. Encryption remains AES 128 CTR, but keys generation is different.

These slides (camera_jailbreak_v2.pdf) are the full version (TLP AMBER) of the slides been presented at BeeRump 2022 conference https://www.rump.beer/2022/category/programme.html, where only a GREEN version has been published in 2022 on the website : https://www.rump.beer/2022/slides/camera_jailbreak_v2_green.pdf.

**No keys are published. You'll need a camera dump to extract seeds and compute signing and decrypting keys.**

## FIR v4 cryptography (40D (2007) and before EOS R (2018))

See [camera_jailbreak_v2.pdf](camera_jailbreak_v2.pdf)

Magic Lantern team is using a similar scheme to forge FIR files which signatures are correct for the camera.

Responsible disclosure have been made to Canon CERT in January 2026, with a python script and keys to forge correct signatures as proof. Do not ask, I'll not release them.

## FIR v5 (EOS R) and v6 (EOS R5)

See ([https://github.com/lclevy/d8_oracle](https://github.com/lclevy/d8_oracle)) and https://github.com/lclevy/d8_oracle/blob/main/hacklu2025_slides/d8_oracle_hacklu_final.pdf

## R1 and R5 Mark II (with Digic Accelerator)

R1 and R5 Mark II are using different format, as it packs several 'legacy FIR' (aka version 6) into one unique file, with a 0x50 bytes header. Current ones embeds three v6 FIRs.

This format (let's call it version 7) has an header explained below :
| offset | type | value | comment |
|---|---|---|---|
| 0 | uint32 | 0x37582111 | magic for this FIR version |
| 4 | uint32 | 3 | likely number of embedded FIR |
| 8 | ? | | |
| 0x10 | uint32 | 0x80000496 | model_id |
| 0x14 | uint8[11] | 1.1.1.8.3.6 | external (1.1.1) and internal version (8.3.6) |
| 0x20 | ? | | |
| 0x30 | uint32[8] | 0x50, 0x7337260, ... | offsets to embedded FIR content |
| 0x50 | bytes | | first FIR |
