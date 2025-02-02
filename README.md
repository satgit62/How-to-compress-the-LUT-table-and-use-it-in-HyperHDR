# How-to-compress-the-LUT-table-and-use-it-in-HyperHDR

There is a new way to Workaround the lack of memory on some LG devices when running multiple LUTs for SDR, HDR and Dolby DV in `/home/root/.hyperhdr/`.
@awawa-dev, has implemented a new compression method in HyperHDR that allows the use of compressed LUTs.

The new compression method compresses the LUTs from 150 MB to less than 10 MB and decompresses them in near real time. 

This requires the new HyperHDR v21.0.0.0beta2 with the ZSTD compression implementation.
Download: https://github.com/satgit62/hyperhdr-webos-loader/actions/runs/12976750759/artifacts/2487359371

The compression method is called ZSTD and HyperHDR should automatically detect that the uncompressed file like **lut_lin_tables_hdr.3d**, **lut_lin_tables_dv.3d** or **lut_lin_tables.3d** does not exist and try to find, load and apply a compressed file with the extension **.zst**.
With normal ZSTD compression, the files shrink from about 150 MB to about 50 MB. But you can make them even smaller. (Smaller than 10MB)

The trick is to prepare each LUT twice with the command 'fsutil file setzerodata offset' before compressing the normal LUTs.This can be done, for example, on a Windows PC via the command line of the command prompt.

Example: You have a directory LUT in `C:/LUT/`. The LUT directory contains the uncompressed LUT files **lut_lin_tables.3d**, **lut_lin_tables_dv.3d** and **lut_lin_tables_hdr.3d**, then execute this command (Windows command line):

```
fsutil file setzerodata offset=0 length=50331648 c:\LUT\lut_lin_tables.3d

fsutil file setzerodata offset=100663296 length=50331648 c:\LUT\lut_lin_tables.3d


fsutil file setzerodata offset=0 length=50331648 c:\LUT\lut_lin_tables_dv.3d

fsutil file setzerodata offset=100663296 length=50331648 c:\LUT\lut_lin_tables_dv.3d


fsutil file setzerodata offset=0 length=50331648 c:\LUT\lut_lin_tables_hdr.3d

fsutil file setzerodata offset=100663296 length=50331648 c:\LUT\lut_lin_tables_hdr.3d

```
# This is the output:

![cmd](https://github.com/user-attachments/assets/c3b1211b-3665-4160-9d4d-06eef444dc9c)

This allows you to compress more efficiently without losing any data.

# 7-Zip-zstd:

You will need 7-Zip-zstd on your PC, which you can download and install from https://github.com/mcmilk/7-Zip-zstd.

![7 zip_zsd](https://github.com/user-attachments/assets/bcd1680d-f5bf-4048-a85e-19546d5e43b5)


After installation, use 7-zip ZS. Right-click on the LUT file: 7-zip ZS ⇒ Add to archive.
You should see the 7-zip compression settings.

Select zstd compression.
The recommended compression rate is 17 - maximum. It also works very well with a compression of 22.

# Note: Do not change the suggested filename.
After creating the compressed LUT file, you can delete the uncompressed LUT file.

After compressing the files, the uncompressed LUT files in `/home/root/.hyperhdr/ or /media/developer/apps/usr/palm/services/org.webosbrew.hyperhdr.loader.service/hyperhdr/` must be replaced with the compressed files so that they look like this:

```
lut_lin_tables.3d.zst
lut_lin_tables_hdr.3d.zst
lut_lin_tables_dv.3d.zst
```

This is the result of the decompression time between loading an HDR LUT and switching to loading an SDR LUT on my old 2017 device, and for me, it's a very good compromise:
```
2025-01-25T12:33:09.958Z [FLATBUFSERVER] Setting user LUT filename to: 'lut_lin_tables_hdr.3d'
2025-01-25T12:33:09.958Z [FLATBUFSERVER] Tone mapping: 1
2025-01-25T12:33:09.959Z [FLATBUFSERVER] (FlatBuffersServer.cpp:301) Adding user LUT file for searching: /home/root/.hyperhdr/lut_lin_tables_hdr.3d
2025-01-25T12:33:09.963Z [FLATBUFSERVER] (LutLoader.cpp:73) LUT file found: /home/root/.hyperhdr/lut_lin_tables_hdr.3d.zst (compressed)
2025-01-25T12:33:09.963Z [FLATBUFSERVER] (LutLoader.cpp:84) Index 1 for HDR YUV
2025-01-25T12:33:11.384Z [FLATBUFSERVER] Decompression took 1.421000 seconds
2025-01-25T12:33:11.387Z [FLATBUFSERVER] Found and loaded LUT: '/home/root/.hyperhdr/lut_lin_tables_hdr.3d'
2025-01-25T12:33:11.388Z [FLATBUFSERVER] Setting user LUT filename to: 'lut_lin_tables.3d'
2025-01-25T12:33:11.388Z [FLATBUFSERVER] Tone mapping: 1
2025-01-25T12:33:11.388Z [FLATBUFSERVER] (FlatBuffersServer.cpp:301) Adding user LUT file for searching: /home/root/.hyperhdr/lut_lin_tables.3d
2025-01-25T12:33:11.389Z [FLATBUFSERVER] (LutLoader.cpp:73) LUT file found: /home/root/.hyperhdr/lut_lin_tables.3d.zst (compressed)
2025-01-25T12:33:11.389Z [FLATBUFSERVER] (LutLoader.cpp:84) Index 1 for HDR YUV
2025-01-25T12:33:12.866Z [FLATBUFSERVER] Decompression took 1.477000 seconds
2025-01-25T12:33:12.870Z [FLATBUFSERVER] Found and loaded LUT: '/home/root/.hyperhdr/lut_lin_tables.3d
```

For those who do not have the possibility to compress their own LUT table, I provide mine here for testing.
Simply copy the three compressed LUT files to `/home/root/.hyperhdr/` and delete all other LUTs.

Source: https://github.com/awawa-dev/HyperHDR/pull/1062


