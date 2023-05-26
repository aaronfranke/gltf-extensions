# OMI_vehicle_thruster

## Contributors

* Aaron Franke, Godot Engine.

## Status

Open Metaverse Interoperability Group Draft Proposal

## Dependencies

Written against the glTF 2.0 spec.

Designed to be used together with the `OMI_vehicle_body` spec.

## Overview

This extension allows specifying generic thrusters in glTF scenes.

Thrusters as defined by `OMI_vehicle_thruster` are very general-purpose, they emit a force and can optionally gimbal. `OMI_vehicle_thruster` can be used to define rocket engines, jet engines, control thrusters, or any other kind of thruster. This extension does not define fuel, power, or any other resource that the thruster may consume.

Thruster glTF nodes can be added as a descendant of a vehicle body glTF node. A vehicle body is defined as a glTF node with the `OMI_vehicle_body` extension. Then thrusters can be added to the vehicle by adding descendant nodes with the `OMI_vehicle_thruster` extension. Thrusters should be specified on their own glTF nodes as descendants of a vehicle body, not on the same glTF node as the vehicle; this so that they may have their own transform relative to the vehicle, including their own position, and so that they and may optionally rotate for gimbal.

A thruster's force is applied in the local +Z direction of the glTF node, pushing the vehicle in the thruster's local +Z direction, which mimics a thruster shooting out propellant in the thruster's local -Z direction. The forward direction a vehicle is the local +Z direction. A thruster with no rotation relative to the vehicle will push the vehicle forward in its local +Z direction. To apply thrust in other directions, the thruster must be rotated relative to the vehicle.

Thrusters can optionally gimbal, which means they can rotate to apply thrust in different directions. The maximum angle the thruster can rotate is defined by the `"gimbal"` property. When a thruster gimbals, the glTF node MUST rotate to match the gimbal, such that the local +Z direction of the glTF node is always the direction the thruster is applying thrust.

Thrusters are invisible by default. To give a thruster a visual appearance, you may wish to add another glTF node with a mesh as a child of the thruster.

### Example:

The file [simple_car.gltf](examples/simple_car.gltf) defines a low-poly simple car with 4 wheels, and 4 seats, one of which is the pilot seat.

More example assets can be found in the [`examples/`](examples/) folder. All of these examples use `OMI_collider` and `OMI_physics_body`.

## glTF Schema Updates

This extension consists of three new data structures for defining thruster specifications on the root glTF document and referencing them on a glTF node. The main data structure defines thruster parameters and is what most of this document describes. The second data structure uses the key `"OMI_vehicle_thruster"` in the document-level `"extensions"` which contains a list of the main data structures of thruster parameters. The third data structure uses the key `"OMI_vehicle_thruster"` in the node-level `"extensions"` which contains an index of the thruster parameters to use from the document-level thruster list.

The extension must also be added to the glTF's `extensionsUsed` array and because it is optional, it does not need to be added to the `extensionsRequired` array.

### Property Summary

The rest of the document, including this summary, defines the properties for the main data structure.

|                | Type     | Description                                           | Default value        |
| -------------- | -------- | ----------------------------------------------------- | -------------------- |
| **force**      | `number` | The maximum thrust force in Newtons (kg⋅m/s²).        | Required, no default |
| **gimbal**     | `number` | The maximum angle the thruster can rotate in radians. | 0.0                  |

### Force

The `"force"` property is a number that defines the maximum thrust force in Newtons (kg⋅m/s²) that the thruster can provide. This property is required and has no default value.

Valid values are positive numbers. Rocket engines in real life have a wide variety of force amounts. Typical rocket engines are usually between one thousand and one million Newtons, but may be much less in the case of small control thrusters, or much more in the case of large main engines. The desired force of a thruster also varies a lot depending on the mass of the vehicle and the desired acceleration and control over it. This wide variety means that there is no sane choice for a default value, so there is no default value.

The active force of the thruster is implementation-defined. The force may be applied when the pilot tries to move the vehicle, such as a user pressing buttons, or an NPC controlling the vehicle.

### Gimbal

The `"gimbal"` property is a number that defines the maximum angle in radians that the thruster can rotate. If not specified, the default value is 0.0 radians, which means the thruster cannot rotate.

Valid values are between 0.0 and τ/2 radians (π or 3.14159265... radians, or 180 degrees). Sane values are between 0.0 and 1.0 radians, while realistic values are between 0.0 and 0.2 radians.

What causes gimbal to occur is implementation-defined. The gimbal may occur when the pilot tries to rotate the vehicle, such as a user pressing buttons, or an NPC controlling the vehicle.

### JSON Schema

See [thruster.schema.json](schema/wheel.schema.json) for the main wheel parameter schema, [glTF.OMI_vehicle_wheel.schema.json](schema/glTF.OMI_vehicle_wheel.schema.json) for the document-level list of wheel parameters, and [node.OMI_vehicle_wheel.schema.json](schema/node.OMI_vehicle_wheel.schema.json) for the node-level collider selection.

## Known Implementations

* 

## Resources:

* Kerbal Space Program wiki on engines: https://wiki.kerbalspaceprogram.com/wiki/Parts#Engines
