|  | Hashing | MAC | Digital Signature | Symmetric encryption | Asymmetric encryption |
| :---: |:---: | :---: | :---: | :---: | :---: |
| __Confidentiality__ | | | | `X`<sup>1</sup> | `X` |
| __Integrity__ | `X` | `X` | `X` | |  |
| __Authentication__ | | `X` | `X` | | |
| __Non-repudiation__ | | | `X` | | |

<sup>1</sup> In order for symmetric encryption to provide confidentiality, the session key must be already shared securely. This is not possible with symmetric encryption alone. Usually asymmetric encryption is used to securely perform key sharing for symmetric encryption.