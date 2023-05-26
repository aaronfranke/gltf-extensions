# OMI_vehicle_body

## Contributors

* Aaron Franke, Godot Engine.

## Status

Open Metaverse Interoperability Group Draft Proposal

## Dependencies

Written against the glTF 2.0 spec.

Depends on the `OMI_physics_body` spec, and is designed to be used with `OMI_vehicle_wheel` and `OMI_seat`.

## Overview

This extension allows for specifying additional specialized details of a vehicle body.

To use this extension, place `OMI_vehicle_body` on the same glTF node as an `OMI_physics_body`. The `OMI_vehicle_body` extension can be empty if it does not need any of the parameters defined, its existence indicates that the glTF node is a vehicle body.

The front of the vehicle faces +Z. The pilot seat is expected to face +Z to have the pilot facing towards the front of the vehicle. A stable ground vehicle like a car is expected to have its parts mostly above the center of mass, so that the vehicle is bottom-heavy.

### Example:

The file [minimal_vehicle_body.gltf](examples/minimal_vehicle_body.gltf) defines a very minimal vehicle with no wheels or other functional parts. It only has a body, collider, and pilot seat.

In order to make the vehicle functional, additional parts would need to be attached, such as a wheel or thruster. See the example files in the `OMI_vehicle_wheel` spec. A vehicle without wheels or thrusters will not have a way to propel itself.

## glTF Schema Updates

This extension consists of a new `OMI_vehicle_body` data structure which can be added to the extensions of an `OMI_physics_body` glTF node.

The extension must also be added to the glTF's `extensionsUsed` array and because it is optional, it does not need to be added to the `extensionsRequired` array.

The extension is intended to be used together with `OMI_seat`.

### Property Summary

|                     | Type        | Description                                                              | Default value |
| ------------------- | ----------- | ------------------------------------------------------------------------ | ------------- |
| **pilotSeat**       | `number`    | The index of the `OMI_seat` glTF node to use as the pilot / driver seat. | -1 (no seat)  |

### Pilot Seat

The `"pilotSeat"` property is an integer that defines the index of the glTF node to use as a pilot seat, also known as driver's seat in the context of cars. The node that `"pilotSeat"` points to should be a seat as defined by the `OMI_seat` extension. If not specified or set to -1, this vehicle does not have a pilot seat.

A character sitting in the pilot seat is called the pilot. The pilot should be given control of the vehicle through whatever control scheme the implementation desires. The pilot seat is expected to face +Z to have the pilot facing towards the front of the vehicle. If an implementation does not allow for characters to pilot the vehicle, this property can be ignored.

### JSON Schema

See [schema/node.OMI_physics_body.schema.json](schema/node.OMI_physics_body.schema.json).

## Known Implementations

* Godot Engine: https://github.com/omigroup/omi-godot/pull/2

## Resources:

* None so far.
