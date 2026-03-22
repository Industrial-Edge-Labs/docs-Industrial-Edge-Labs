# Industrial MLOps Data Lake Pipeline

## Scope

This document describes the external architecture of the [industrial-mlops-data-lake-pipeline](https://github.com/Industrial-Edge-Labs/industrial-mlops-data-lake-pipeline) repository inside the [docs-Industrial-Edge-Labs](https://github.com/Industrial-Edge-Labs/docs-Industrial-Edge-Labs) repository.

The node ingests inspection-side anomaly evidence, persists a structured corpus, and generates OTA manifests once the retraining threshold has been satisfied. The default runtime is portable and stores data locally, while an optional MinIO mode preserves S3-compatible behavior.

## Position In The Flow

```mermaid
flowchart LR
    VIE[Industrial Visual Inspection Engine] -->|Anomaly evidence upload| MLOPS[Industrial MLOps Data Lake Pipeline]
    VPE[Event-Driven Vision Processing Engine] -.->|Selected low-confidence imagery| MLOPS
    MLOPS -->|OTAManifest| FLEET[Edge Device Fleet Manager]
    MLOPS -->|Training corpus| Trainers[Retraining and annotation jobs]
```

## Execution Model

```mermaid
sequenceDiagram
    autonumber
    participant Inspection as Inspection Engine
    participant Lake as Data Lake API
    participant Sweeper as Retraining Trigger
    participant Fleet as Fleet Manager

    Inspection->>Lake: POST /api/v1/datalake/upload
    Lake->>Lake: Persist image artifact and metadata index
    Sweeper->>Lake: Read local corpus and threshold status
    Sweeper->>Sweeper: Generate deterministic artifact version and hash
    Sweeper->>Fleet: POST /api/v1/nodes/ota-manifest
```

## Primary Contracts

### Upload Form Fields

```mermaid
classDiagram
    class AnomalyUploadForm {
        string device_id
        uint64 timestamp_ns
        uint64 frame_id
        float confidence
        uint32 class_id
        float x
        float y
        float width
        float height
        string source_repo
        string model_version
        file anomaly_image
    }
```

### OTA Manifest

```mermaid
classDiagram
    class OTAManifest {
        string latest_firmware
        string download_url
        string sha256_hash
        bool force_restart
        string published_at
        string model_version
        string artifact_kind
        string notes
    }
```

## Runtime Notes

- Local filesystem mode persists both binary artifacts and a deterministic `index.json` metadata ledger.
- MinIO mode keeps the same metadata ledger while uploading the binary artifact to an S3-compatible bucket.
- Retraining manifest generation is deterministic from the currently stored corpus.

## Recommended Next Steps

1. Add signed model artifact verification before production OTA publication.
2. Add an annotation status field if human review enters the active learning loop.
3. Wire the upload client directly into [industrial-visual-inspection-engine](https://github.com/Industrial-Edge-Labs/industrial-visual-inspection-engine) once the GPU inference runtime is ready to export crops asynchronously.
