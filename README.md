# Python bindings for F.A.C.C.T. XDR REST API

**Latest Version: 1.0.0**

## Description

The F.A.C.C.T. XDR Python Client enables you to fully integrate F.A.C.C.T. XDR MDP into your malware analysis framework.
F.A.C.C.T. XDR MDP is a Malware Detonation & Research platform designed for deep dynamic analysis and enhanced indicators extraction.

You can use this library with

 * [F.A.C.C.T. XDR Cloud](https://xdr.facct.ru) — our Cloud hosted instance
 * [On-premise installations of F.A.C.C.T. XDR](https://www.facct.ru/products/managed-xdr/) — for even more power and privacy

 ## License

 The code is written in Python and licensed under MIT.

 ## Requirements

 * python 3.6 or higher

## Getting Started

### Installation

    pip install pyxdr

For upgrading `pyxdr` to a more recent version, use
    
    pip install --upgrade pyxdr

### API Key

In order to perform any queries via the API, you will need to get the API token for your F.A.C.C.T. XDR user.
1. Open F.A.C.C.T. XDR web interface.
2. Navigate to "Profile" and click "Generate Auth Token".
3. Copy this token. This is your API Key.

### Sample Code

1. Let's start by sending some file ("sample.exe") for analysis:
```
from pyxdr import MDP

mdp = MDP("MY_API_KEY")
analysis = mdp.upload_file(open("sample.exe", "rb"))
```
2. If you want to detonate some URL, use the next method:
```
analysis = mdp.upload_url("https://very-malicious-url.com")
```
Now we have the `analysis` object.
To update analysis status and get info about it, use the next method:
```
info = analysis.get_info(extended=True)
```
**Notice**: parameter `extended` allows you to get full or short info about analysis process. The short version of the information is as follows:
```
{
    "status": "IN PROGRESS" | "FINISHED" | "FAILED",
    "verdict": None | True | False,
    "report_url": "https://...",
    "error": "Some error"  # optional field only for "FAILED" status
}
```
If the "verdict" is `True` then object is malicious.
**Notice**: XDR need some time to generate the report url. Until it happens, the response will not contain this field.

3. You can get full report as a dictionary:
```
report = analysis.get_report()
```
4. There is a way to download some detonation artifacts and the report:
```
archived_report = analysis.export_report()  # Export report as .tar.
pdf_report = analysis.export_pdf_report()   # Export report as PDF
pcap = analysis.export_pcap()               # Export all network activity as .pcap file.
screen_video = analysis.export_video()      # Export the screen-video of the detonation process.
```

**Notice**: If there is no artifact, all this methods raise `ObjectNotFoundError`.

5. You can check some hash reputation with this method:
```
reputation = mdp.get_hash_reputation("md5", "ac55cf33c4691f863bfb3af8c06a7244")
```
You can get reputation for `md5`, `sha1`, `sha256` hash types.
The method returns a dict object:
```
{
    "found": true | false,
    "verdict": true | false,
    "malware_families": [],
    "score": float in [0; 100]
}
```