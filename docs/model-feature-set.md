# MRD Model Feature Set Extension

This document defines a hardware-targeting extension for AI/ML model workflows.

Goal: make generated model packages deterministic for a specific `MCU/MPU/SOM` and board context while preserving MRD as the hardware source of truth.

## Design Principles

- Backward compatible: existing MRD files remain valid.
- Hardware-first: model capability claims must map to concrete device/board constraints.
- Cross-spec friendly: references to EIP/MDP/SDP are links, not hard dependencies.
- Toolable: all sections are structured to support validation and code generation.

## New Top-Level Sections

Add these optional sections to MRD:

- `modelTargets`
- `modelFeatureSets`
- `modelRuntimeProfiles`
- `modelIoBindings`
- `modelValidation`

## 1) `modelTargets`

Declares which exact parts and variants this MRD model-target profile applies to.

```json
{
  "modelTargets": [
    {
      "id": "nxp_mcxn947",
      "vendor": "NXP",
      "family": "MCXN",
      "partNumber": "MCXN947",
      "package": "LQFP144",
      "siliconRevisions": ["A0", "A1"],
      "board": {
        "name": "FRDM-MCXN947",
        "sku": "FRDM-MCXN947"
      }
    }
  ]
}
```

## 2) `modelFeatureSets`

Describes reusable feature sets that a model may require. A single feature set can support many applications (tracking, anomaly detection, prediction, etc.).

```json
{
  "modelFeatureSets": [
    {
      "id": "adaptive_reasoning_generic",
      "name": "Adaptive Reasoning",
      "description": "Self-learning runtime feature pipeline with optional confidence output.",
      "category": "adaptive_reasoning",
      "compatibleTargets": ["nxp_mcxn947"],
      "pipeline": {
        "stages": [
          { "name": "input", "kind": "input" },
          { "name": "normalize", "kind": "prep" },
          { "name": "model", "kind": "inference" },
          { "name": "post", "kind": "post" }
        ]
      },
      "resourceEnvelope": {
        "flashKB": { "min": 64, "max": 512 },
        "ramKB": { "min": 32, "max": 256 },
        "cpuMHz": { "min": 80, "recommended": 150 }
      }
    }
  ]
}
```

## 3) `modelRuntimeProfiles`

Defines deployable runtime profiles for a feature set on specific targets.

```json
{
  "modelRuntimeProfiles": [
    {
      "id": "ar_mcxn947_profile_1",
      "featureSetId": "adaptive_reasoning_generic",
      "targetId": "nxp_mcxn947",
      "runtime": {
        "executionClass": "MCU",
        "scheduler": ["baremetal", "freertos"],
        "numeric": ["float32", "q15"],
        "acceleration": ["dsp", "simd"]
      },
      "limits": {
        "maxInputs": 32,
        "maxOutputs": 8,
        "maxParameters": 50000,
        "maxLatencyUs": 3000
      }
    }
  ]
}
```

## 4) `modelIoBindings`

Binds abstract model signals to concrete hardware sources and sinks.

```json
{
  "modelIoBindings": [
    {
      "id": "binding_default",
      "targetId": "nxp_mcxn947",
      "inputs": [
        {
          "name": "accel_x",
          "type": "float",
          "source": {
            "kind": "sensor",
            "instance": "BMI270",
            "interface": "I2C1",
            "registerPath": "ACC_X"
          }
        }
      ],
      "outputs": [
        {
          "name": "anomaly_score",
          "type": "float",
          "sink": {
            "kind": "firmware_var",
            "symbol": "health.anomaly_score"
          }
        }
      ]
    }
  ]
}
```

## 5) `modelValidation`

Defines machine-checkable readiness gates for generation and build.

```json
{
  "modelValidation": {
    "requiredDocs": ["reference-manual"],
    "requiredFields": ["device", "modelTargets", "modelFeatureSets"],
    "requiredProjectFields": ["hardware.partNumber"],
    "consistencyRules": [
      "modelRuntimeProfiles.targetId in modelTargets.id",
      "modelRuntimeProfiles.featureSetId in modelFeatureSets.id",
      "modelIoBindings.targetId in modelTargets.id"
    ]
  }
}
```

## Recommended Metadata for Integration Docs

To generate useful datasheets automatically, tools should include:

- model/package identity: name, packageId, semantic version
- hardware target identity: vendor, family, part number, board
- exact input/output contract: name, units, rates, expected ranges
- integration steps: init, per-cycle call, output readout, confidence/quality handling
- timing + memory profile per runtime profile
- traceability links: reference manual sections, errata IDs, source commit/hash

## Compatibility Notes

- `modelFeatureSets` is intentionally generic and application-neutral.
- Application-specific text should live in higher-level package specs or project metadata.
- For self-learning systems, omit training-only fields and use adaptation/state fields instead.
