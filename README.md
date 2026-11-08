# Investigating DJI Mavic Air Flight Evidence

---

## 1. Project Overview

This repository documents a digital forensic examination of Android logical evidence associated with the DJI GO 4 mobile application and DJI Mavic Air flight-related artefacts.

The investigation focused on identifying, preserving, examining and correlating digital artefacts that could provide information about drone application activity, flight records, cached video, application notifications and associated metadata.

The examination involved extracting an Android logical evidence archive, verifying its integrity using SHA-256 hashing, identifying relevant DJI GO 4 artefacts, conducting preliminary binary analysis, examining video metadata and constructing a provisional timeline from the available timestamps.

The investigation also documents the limitations encountered during the examination, particularly the unresolved proprietary flight-record formats and discrepancies between different timestamp and video-duration sources.

This repository contains the original evidence archive, extracted working evidence, investigation outputs, supporting media and the submitted case study report.

## 2. Investigation Objectives

The primary objectives were to:

- Establish an organised forensic working environment.
- Preserve the original Android logical evidence and conduct examination using a separate working copy.
- Verify evidence integrity using cryptographic hashing.
- Identify and catalogue DJI GO 4 application artefacts.
- Locate flight records, DAT records, cached video, metadata, application logs and cached images.
- Examine relevant file structures and metadata.
- Investigate the relationship between the available flight records and video evidence.
- Construct a provisional timeline of relevant application events.
- Document findings, unresolved issues and limitations.

## 3. Scope of Examination

The examination concentrated on the DJI GO 4 application directory within the extracted Android logical evidence.

The principal artefact locations included:

| Evidence category | Location |
|---|---|
| Flight records | `DJI/dji.go.v4/FlightRecord/` |
| DAT flight records | `DJI/dji.go.v4/FlightRecord/MCDatFlightRecords/` |
| Cached video | `DJI/dji.go.v4/DJI_RECORD/` |
| Video metadata | `DJI/dji.go.v4/DJI_RECORD/` |
| Analytics data | `DJI/dji.go.v4/DJI_RECORD/analytics/` |
| Cached images | `DJI/dji.go.v4/CACHE_IMAGE/` |
| Application logs | `DJI/dji.go.v4/LOG/` |

The investigation was limited to the evidence available in the supplied Android logical archive. A raw internal microSD image and an EWF image were not available for examination.

## 4. Forensic Methodology

The investigation followed a structured digital forensic workflow.

### 4.1 Case Environment Preparation

A dedicated case directory was established on Kali Linux to separate original evidence, working copies, analytical outputs, screenshots and reporting materials.

The directory structure was organised to prevent examination activities from being conducted directly against the preserved original archive.

### 4.2 Evidence Preservation and Integrity Verification

The Android logical archive, `Android_Logical.zip`, was copied into the `originals/` directory.

A separate working copy was created under `working/`.

SHA-256 hashing was performed on both archives using the Linux `sha256sum` utility.

Both files produced the following hash:

```text
9a352b282d832556eb7cb73010ea281f9946d889438ce07d9bbee3b64229285d
```

The matching hashes establish that the original and working archive files were byte-for-byte identical at the time of verification.

### 4.3 Evidence Extraction

The working archive was extracted using the Linux `unzip` utility.

The resulting Android logical evidence contained, among other directories, the `property` and `sdcard` directories.

Subsequent examination focused on the DJI GO 4 application data under:

```text
Android_Logical/Android_Logical/sdcard/DJI/dji.go.v4/
```

### 4.4 Evidence Identification and Inventory

A recursive file inventory was generated using the Linux `find` utility.

The inventory identified **220 files** within the DJI GO 4 application directory.

The inventory recorded file paths and file sizes and was saved as:

```text
evidence/dji_evidence_inventory.txt
```

The identified artefacts included flight records, DAT files, cached media, INFO metadata, analytics files, cached images and application logs.

### 4.5 Cryptographic Hashing

SHA-256 hashes were calculated for six principal DJI GO 4 artefacts in addition to the original and working Android archives.

The resulting hash values were documented in the investigation evidence register.

Hashing provided reference values for subsequent integrity verification. It does not independently establish the authenticity of the original evidence or the accuracy of embedded metadata.

### 4.6 Binary and Metadata Examination

The flight records and associated files were subjected to preliminary structural examination.

The examination included file identification, hexadecimal inspection, printable-string extraction and binary comparison.

The cached MP4 video was examined using FFprobe to establish its video-stream properties, frame count and duration.

The associated INFO file was examined to identify recorded capture information, technical properties, GPS fields and synchronisation metadata.

### 4.7 Timeline Construction and Cross-Source Correlation

Relevant timestamps from the DJI notification log, INFO metadata and filesystem metadata were compiled into a provisional timeline.

The timeline distinguished application-recorded timestamps from filesystem modification timestamps and explicitly documented unresolved timezone differences.

The available artefacts were compared to identify possible relationships without assuming that every file represented the same flight event.

---

## 5. Tools and Technologies

The examination was conducted primarily within Kali Linux using command-line utilities and media analysis tools.

| Tool | Purpose |
|---|---|
| Kali Linux | Forensic examination environment |
| `unzip` | Extraction of the Android logical archive |
| `sha256sum` | Cryptographic integrity verification |
| `find` | Recursive evidence identification and inventory |
| `file` | File-type identification |
| `stat` | Filesystem metadata examination |
| `xxd` | Hexadecimal examination of binary artefacts |
| `strings` | Extraction of printable character sequences |
| `cmp` | Binary comparison of DAT records |
| FFprobe | MP4 stream and metadata examination |
| GNU/Linux shell utilities | Evidence processing and documentation |
| GNU Octave / SciPy | Attempts to load and interpret DAT records |

The attempts to interpret the DAT records using Octave and SciPy were unsuccessful. Their internal structures therefore remain unresolved.

---

## 6. Principal Evidence Identified

The examination identified the following principal artefacts.

### 6.1 DJI TXT Flight Record

**Filename:**

```text
DJIFlightRecord_2018-06-19_[14-50-34].txt
```

- File size: 555,776 bytes.
- SHA-256: `0a9f1816d52a98f840f6e67c08a14473ae5a0a2e75e9484d5de66368c4fd4c4a`
- Identified as generic binary data during preliminary examination.
- Initial hexadecimal and printable-string examination did not reveal interpretable flight information.
- Further format-aware decoding is required.

### 6.2 DAT Flight Records

Two DAT files were identified:

**FLY057**

```text
18-06-19-02-47-38_FLY057.DAT
```

- File size: 4,002,791 bytes.
- SHA-256: `06130820a4a813bf21f7286150bd3c83084105fecc899bb4d8534895fa2df3a1`

**FLY058**

```text
18-06-19-02-59-05_FLY058.DAT
```

- File size: 1,104,150 bytes.
- SHA-256: `fdfccf0c5b1d90d189c19032bb34e151914f42fba42c7994d0492cf327ade07a`

Both records displayed a common initial 140-byte sequence. Binary comparison established that they diverge from byte position 141.

The files also contained the printable strings:

```text
BUILD Apr 24 2018 18:39:53
DJI_LOG_V3
```

Although the Linux `file` utility initially identified the records as MATLAB version 4 MAT-files, attempts to load them using Octave and SciPy were unsuccessful. This identification is therefore treated as unverified.

No reliable interpretation of their internal flight parameters was established.

### 6.3 Cached MP4 Video

**Filename:**

```text
2018_06_19_14_50_39.mp4
```

- File size: 57,791,765 bytes.
- SHA-256: `2ae2a4f186803ec592b0af6284634e592c5079395e99f7df3edd583f8e72ab00`

FFprobe identified:

- Video codec: H.264/AVC.
- Resolution: 1280 × 720 pixels.
- Nominal frame rate: 30 FPS.
- Frame count: 7,045.
- Reported duration: approximately 240.972 seconds.

Eight representative video frames were extracted for visual examination and documentation.

### 6.4 Associated INFO Metadata

**Filename:**

```text
2018_06_19_14_50_39.info
```

- File size: 1,441 bytes.
- SHA-256: `a42bfeb57c5fff850bc059e10eb9b7a4f7b84887feb71e65464099d1e0fd0b4a`

The metadata contained:

- Capture date: 19 June 2018 at 14:50:39.
- EndTimeMsec: 234,833 milliseconds.
- Drone frame rate: 30 FPS.
- Drone dimensions: 1920 × 1080.
- Local video dimensions: 1280 × 720.
- Device manufacturer: DJI.
- Synchronisation timing arrays.
- GPS and altitude fields.

The precise GPS coordinates are not reproduced in this README.

### 6.5 DJI GO 4 Application Notification Log

**Filename:**

```text
19-06-2018-0K1DF313BD4PR1
```

- File size: 828 bytes.
- SHA-256: `cb9671659ae2ff11b0fd3dd846e8702a81366479b04f3e2442300b2211bcff3e`

The examination identified eight timestamped warnings:

1. SD-card write speed warning.
2. Maximum flight distance warning.
3. Weak signal warning.
4. High wind velocity warning.
5. A second maximum flight distance warning.
6. Obstacle sensing disabled during landing warning.
7. Backward obstacle sensing malfunction warning.
8. Weak GPS signal and Attitude mode warning.

These notifications provide contextual information about application activity. They do not independently prove specific physical aircraft events.

---

## 7. Key Investigation Findings

### 7.1 Evidence Integrity

The original and working Android logical archives produced matching SHA-256 values.

This established that the working archive was a byte-for-byte copy of the original archive at the time of verification.

### 7.2 DJI GO 4 Artefact Identification

A recursive inventory identified 220 files within the DJI GO 4 application directory.

The inventory provided a structured basis for selecting relevant artefacts for detailed examination.

### 7.3 DAT Record Comparison

FLY057 and FLY058 shared an identical initial 140-byte sequence but had different file sizes and SHA-256 values.

The first binary difference occurred at byte position 141.

The available analysis did not establish the meaning of the differing binary fields or the precise relationship between the two records.

### 7.4 Video Duration Discrepancy

The INFO metadata recorded a duration of 234.833 seconds.

FFprobe reported a duration of approximately 240.972 seconds for the MP4 file.

This represents a difference of approximately 6.139 seconds.

The discrepancy was documented rather than attributed to a particular cause.

### 7.5 Timestamp Correlation

The notification log contained eight warnings between 14:49:39 and 15:02:10 on 19 June 2018.

The INFO metadata recorded a capture date of 14:50:39, while its header timestamp explicitly stated EDT.

Filesystem timestamps also contained a UTC−04:00 offset.

The timezone basis of the application event timestamps was not independently established. Consequently, a definitive unified UTC chronology could not be confirmed.

### 7.6 Application Log Correlation

A search of the examined application logs for FLY057, FLY058, DJI_LOG_V3 and the associated video filename did not produce matching entries.

This did not establish that the artefacts were unrelated; relevant associations may be recorded in binary data or under different identifiers.

---

## 8. Provisional Timeline

The provisional timeline was constructed from the available application notification timestamps, video metadata and filesystem metadata.

Selected events include:

| Recorded time | Event |
|---|---|
| 14:49:39 | SD-card write speed warning |
| 14:50:39 | Video capture date recorded |
| 14:52:06 | Maximum flight distance warning |
| 14:52:08 | Weak signal warning |
| 14:52:14 | High wind velocity warning |
| 14:53:51 | Maximum flight distance warning |
| 14:55:33 | Obstacle sensing disabled during landing warning |
| 14:56:24 | Backward obstacle sensing malfunction warning |
| 15:02:10 | Weak GPS signal and Attitude mode warning |

These entries represent recorded timestamps. They should not be interpreted as a definitive sequence of physical aircraft events until their timezone conventions and underlying sources have been independently validated.

The complete timeline is available in:

```text
evidence/provisional_timeline.csv
```

---

## 9. Repository Structure

The repository is organised as follows:

```text
Investigating-DJI-Mavic-Air-Flight-Evidence/
│
├── README.md
│
├── Investigating DJI Mavic Air Flight Evidence.pdf
│
├── .gitattributes
│
├── originals/
│   └── Android_Logical.zip
│
├── working/
│   ├── Android_Logical.zip
│   └── Android_Logical/
│       └── Android_Logical/
│           ├── property/
│           └── sdcard/
│               └── DJI/
│                   └── dji.go.v4/
│
├── evidence/
│   ├── dji_evidence_inventory.txt
│   ├── evidence_log.csv
│   └── provisional_timeline.csv
│
└── media/
    ├── dji_cached_images_contact_sheet.jpg
    │
    └── video_frames/
        ├── frame_001.jpg
        ├── frame_002.jpg
        ├── frame_003.jpg
        ├── frame_004.jpg
        ├── frame_005.jpg
        ├── frame_006.jpg
        ├── frame_007.jpg
        └── frame_008.jpg
```

**Directory descriptions**

- `originals/` – Original Android logical evidence archive preserved as supplied.
- `working/` – Working copy of the archive and extracted Android logical evidence used during examination.
- `evidence/` – Evidence inventory, evidence register and provisional timeline.
- `media/` – Contact sheet of cached DJI images and representative extracted video frames.
- `Investigating DJI Mavic Air Flight Evidence.pdf` – Submitted case study report.
- `.gitattributes` – Git LFS tracking configuration for selected large files.

The repository includes the complete original and working evidence folders, not only the derived analytical outputs.

---

## 10. Evidence Register and Integrity References

The investigation's evidence register is maintained in:

```text
evidence/evidence_log.csv
```

The register records evidence identifiers, descriptions, source locations, file sizes, SHA-256 values, examination methods and findings.

The SHA-256 value of the original Android logical archive and its working copy is:

```text
9a352b282d832556eb7cb73010ea281f9946d889438ce07d9bbee3b64229285d
```

The individual artefact hashes are documented in the evidence register.

---

## 11. Forensic Considerations

This project follows basic forensic handling principles:

- Preserve original evidence separately from working copies.
- Use cryptographic hashes to establish integrity reference values.
- Document the source and location of examined artefacts.
- Maintain an evidence inventory and examination record.
- Distinguish observed facts from assumptions and interpretations.
- Record unresolved discrepancies and limitations.
- Avoid making conclusions that are not supported by the available evidence.

The repository is an educational case study and should not be treated as a substitute for a full evidential acquisition, validated forensic examination or expert witness report.

---

## 12. Reproducing Selected Examination Steps

The following commands illustrate selected stages of the examination.

**Verify archive integrity:**

```bash
sha256sum originals/Android_Logical.zip
sha256sum working/Android_Logical.zip
```

**Generate a DJI GO 4 file inventory:**

```bash
find working/Android_Logical/Android_Logical/sdcard/DJI/dji.go.v4 \
-type f -printf '%p | %s bytes\n' | sort
```

**Identify a file:**

```bash
file path/to/evidence_file
```

**Examine binary content:**

```bash
xxd path/to/evidence_file | head
```

**Extract printable strings:**

```bash
strings path/to/evidence_file
```

**Compare two DAT files:**

```bash
cmp -l FLY057.DAT FLY058.DAT | head
```

**Examine video metadata:**

```bash
ffprobe -hide_banner 2018_06_19_14_50_39.mp4
```

These commands are examples of the examination workflow. Paths should be adjusted to the relevant evidence location.

---

## 14. Git Large File Storage

Selected large evidence files are managed using Git Large File Storage (Git LFS).

The tracked files include:

- `originals/Android_Logical.zip`
- `working/Android_Logical.zip`
- `working/Android_Logical/Android_Logical/sdcard/DJI/dji.pilot/DJI_RECORD/2017_10_10_10_02_47.mp4`

Git LFS allows large files to be stored and versioned separately from ordinary Git objects.

To clone the repository with its LFS-managed evidence:

```bash
git lfs install
git clone https://github.com/omolaraann/Investigating-DJI-Mavic-Air-Flight-Evidence.git
```

After cloning, retrieve the LFS objects if they have not been downloaded automatically:

```bash
git lfs pull
```

---

## 15. Responsible Use and Evidence Handling Notice

The repository contains original source evidence and derived forensic artefacts obtained for this educational investigation.

Although the source material was publicly available, some files may contain device identifiers, GPS information, timestamps or other metadata.

Users should consider the sensitivity of embedded metadata before redistributing individual files or using the material outside its educational context.

Any further examination should preserve the original files and document changes made to working copies.

---

## 16. Conclusion

This case study demonstrates a structured approach to examining Android logical evidence associated with DJI GO 4 and DJI Mavic Air flight-related artefacts.

The investigation successfully established an organised forensic workspace, verified the integrity of the Android logical archive, identified 220 DJI GO 4 files, examined principal flight and video artefacts, generated SHA-256 reference values and constructed a provisional timeline.

Although several flight-record structures and cross-source relationships remain unresolved, the examination documented the available findings and limitations without presenting unsupported conclusions.

The repository preserves the investigation materials and provides a reference for further learning and independent examination of DJI-related digital evidence.

---

**Repository:** [Investigating DJI Mavic Air Flight Evidence](https://github.com/omolaraann/Investigating-DJI-Mavic-Air-Flight-Evidence)

**Author:** Kafayat Omolara Animashawun  
