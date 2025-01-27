# How-to-compress-the-LUT-table-and-use-it-in-HyperHDR

There is a new way to work around the lack of memory on some LG devices when running multiple LUTs for SDR, HDR and Dolby DV in `/home/root/.hyperhdr/`.
awawa-dev, has implemented a new compression method in HyperHDR that allows the use of compressed LUTs.

The new compression method compresses the LUTs from 150 MB to less than 10 MB and decompresses them in near real time. This requires the new HyperHDR with the ZSTD compression implementation.
Download: https://github.com/satgit62/hyperhdr-webos-loader/actions/runs/12976750759/artifacts/2487359371

The compression method is called ZSTD and HyperHDR should automatically detect that the uncompressed file like lut_lin_tables_hdr.3d, lut_lin_tables_dv.3d or lut_lin_tables.3d does not exist and try to find, load and apply a compressed file with the extension .zst.
With normal ZSTD compression, the files shrink from about 150 MB to about 50 MB. But you can make them even smaller. (Smaller than 10MB)

The trick is to prepare each LUT twice with the command 'fsutil file setzerodata offset' before compressing the normal LUTs.This can be done, for example, on a Windows PC via the command line of the command prompt.

Example: You have a directory LUT in C:/LUT. The LUT directory contains the uncompressed LUT files lut_lin_tables.3d, lut_lin_tables_dv.3d and lut_lin_tables_hdr.3d, then execute this command (Windows command line):

```
fsutil file setzerodata offset=0 length=50331648 c:\LUT\lut_lin_tables.3d

fsutil file setzerodata offset=100663296 length=50331648 c:\LUT\lut_lin_tables.3d


fsutil file setzerodata offset=0 length=50331648 c:\LUT\lut_lin_tables_dv.3d

fsutil file setzerodata offset=100663296 length=50331648 c:\LUT\lut_lin_tables_dv.3d


fsutil file setzerodata offset=0 length=50331648 c:\LUT\lut_lin_tables_hdr.3d

fsutil file setzerodata offset=100663296 length=50331648 c:\LUT\lut_lin_tables_hdr.3d

```
This allows you to compress more efficiently without losing any data.

You will need 7-Zip-zstd on your PC, which you can download and install from https://github.com/mcmilk/7-Zip-zstd.

![7 zip_zsd](https://github.com/user-attachments/assets/bcd1680d-f5bf-4048-a85e-19546d5e43b5)


After installation, use 7-zip ZS. Right-click on the LUT file: 7-zip ZS ⇒ Add to archive.
You should see the 7-zip compression settings.

Select zstd compression.
The recommended compression rate is 17 - maximum. It also works very well with a compression of 22.

#Note: Do not change the suggested filename.
After creating the compressed LUT file, you can delete the uncompressed LUT file.

After compressing the files, the uncompressed LUT files in `/home/root/.hyperhdr/ or /media/developer/apps/usr/palm/services/org.webosbrew.hyperhdr.loader.service/hyperhdr/` must be replaced with the compressed files so that they look like this:

```
lut_lin_tables.3d.zst
lut_lin_tables_hdr.3d.zst
lut_lin_tables_dv.3d.zst
```

For those who do not have the possibility to compress their own LUT table, I provide mine here for testing.
Simply copy the three compressed LUT files to `/home/root/.hyperhdr/` and delete all other LUTs.

Source: https://github.com/awawa-dev/HyperHDR/pull/1062


