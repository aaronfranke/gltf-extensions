# OMI_audio_material

## Contributors

- Aaron Franke, Godot Engine

## Status

Draft

## Dependencies

Written against the glTF 2.0 spec.

Depends on the `OMI_physics_body` spec, which depends on the `OMI_physics_shape` spec.

## Overview

This extension allows defining audio properties on physics materials, which define the acoustic properties for that physics material. This is useful for defining how audio should sound when objects collide with each other, how audio bounces off or passes through an object, how it sounds when characters walk over a surface, and other realistic audio effects in a physics simulation.

### Example:

This example defines a simple audio material with a damping of 0.5 and a transmission of 0.5. It then defines a physics material that uses this audio material, and a node with a collider that uses this physics material.

```json
{
    "asset": {
        "version": "2.0"
    },
    "extensionsUsed": [
        "OMI_audio_material",
        "OMI_physics_body",
        "OMI_physics_shape"
    ],
    "extensions": {
        "OMI_physics_body": {
            "physicsMaterials": [
                {
                    "staticFriction": 0.6,
                    "dynamicFriction": 0.6,
                    "restitution": 0.0,
                    "extensions": {
                        "OMI_audio_material": {
                            "damping": 0.5,
                            "transmission": 0.5,
                        }
                    }
                }
            ]
        },
        "OMI_physics_shape": {
            "shapes": [
                {
                    "type": "box"
                }
            ]
        }
    },
    "nodes": [
        {
            "extensions": {
                "OMI_physics_body": {
                    "collider": {
                        "shape": 0,
                        "physicsMaterial": 0
                    }
                }
            },
            "name": "BoxWithAudioMaterial",
        }
    ],
    "scene": 0,
    "scenes": [{ "nodes": [0] }]
}
```

More example assets can be found in the [examples/](examples/) folder.

## glTF Schema Updates

This extension consists of a new data structure for defining audio properties on a physics material. A glTF file with audio materials may have the key `"OMI_audio_material"` added to a physics material's `"extensions"` to define the acoustic properties of a physics material. As defined by `OMI_physics_body`, the physics material is then referenced by a collider on a node.

The extension must also be added to the glTF's `extensionsUsed` array and because it is optional, it does not need to be added to the `extensionsRequired` array.

### Audio Material Property Summary

|                  | Type     | Description                                                       | Default value |
| ---------------- | -------- | ----------------------------------------------------------------- | ------------- |
| **damping**      | `number` |                                                                   | 0.5           |
| **transmission** | `number` |                                                                   | 0.5           |
| **absorption**   | `object` | A set of absorption coefficients for different sound frequencies. | {}            |

#### Damping

The `"damping"` property is a number. It is on the range of 0.0 to 1.0, inclusive.

#### Transmission

The `"transmission"` property is a number. It is on the range of 0.0 to 1.0, inclusive.

#### Absorption

The `"absorption"` property defines a set of absorption coefficients for different sound frequencies. It is an object with keys as strings representing sound frequencies in Hz, and values as numbers representing the absorption coefficient at that frequency. Each absorption coefficient must be between 0 and 1, inclusive.

For example, concrete has a high absorption rate for high frequencies, causing it to sound dull. Wood can absorb very low and very high frequencies, but reflects mid-range frequencies, giving it a warm sound.

### JSON Schema

See [glTF.OMI_physics_body.material.OMI_audio_material.schema.json](schema/glTF.OMI_physics_body.material.OMI_audio_material.schema.json) for the schema.

## Known Implementations

- None

## Resources:

- List of common absorption coefficients https://www.acoustic.ua/st/web_absorption_data_eng.pdf
