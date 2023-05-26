# OMI_vehicle_wheel

## Contributors

* Aaron Franke, Godot Engine.

## Status

Open Metaverse Interoperability Group Draft Proposal

## Dependencies

Written against the glTF 2.0 spec.

Designed to be used together with the `OMI_vehicle_body` spec.

## Overview

This extension allows specifying vehicle wheels in glTF scenes.

Wheel glTF nodes can be added as a descendant of a vehicle body glTF node. A vehicle body is defined as a glTF node with the `OMI_vehicle_body` extension. Then wheels can be added to the vehicle by adding descendant nodes with the `OMI_vehicle_wheel` extension. Wheels should be specified on their own glTF nodes as descendants of a vehicle body, not on the same glTF node as the vehicle; this so that they may have their own transform relative to the vehicle, including their own position, and so that they and may optionally rotate for steering.

Wheels are circles on the local YZ plane, or optionally cylinders if the width property is specified. To give a wheel a visual appearance, add another glTF node with a mesh as a child of the wheel. The forward direction of both a vehicle and its wheels is the local +Z direction.

### Example:

The file [simple_car.gltf](examples/simple_car.gltf) defines a low-poly simple car with 4 wheels, and 4 seats, one of which is the pilot seat.

More example assets can be found in the [`examples/`](examples/) folder. All of these examples use `OMI_collider` and `OMI_physics_body`.

## glTF Schema Updates

This extension consists of three new data structures for defining wheel specifications on the root glTF document and referencing them on a glTF node. The main data structure defines wheel parameters and is what most of this document describes. The second data structure uses the key `"OMI_vehicle_wheel"` in the document-level `"extensions"` which contains a list of the main data structures of wheel parameters. The third data structure uses the key `"OMI_vehicle_wheel"` in the node-level `"extensions"` which contains an index of the wheel parameters to use from the document-level wheel list.

The extension must also be added to the glTF's `extensionsUsed` array and because it is optional, it does not need to be added to the `extensionsRequired` array.

### Property Summary

The rest of the document, including this summary, defines the properties for the main data structure.

|                         | Type        | Description                                                             | Default value |
| ----------------------- | ----------- | ----------------------------------------------------------------------- | ------------- |
| **radius**              | `number`    | The radius of the wheel in meters.                                      | 0.25          |
| **suspensionStiffness** | `number`    | The stiffness of the suspension, the resistance to moving up or down.   | 40.0          |
| **suspensionTravel**    | `number`    | The distance the suspension can move up or down in meters.              | 0.25          |
| **useForSteering**      | `boolean`   | If true, the wheel should rotate to help the vehicle steer.             | true          |
| **useForTraction**      | `boolean`   | If true, the wheel transfers force to the ground to propel the vehicle. | true          |
| **width**               | `number`    | The width of the wheel in meters.                                       | 0.125         |

### Radius

The `"radius"` property is a number that defines the radius of the wheel in meters. A wheel is a circle centered on the glTF node's origin, with this radius, aligned on the local YZ plane. If not specified, the default radius is 0.25 meters, which is 0.5 meters in diameter.

### Suspension Stiffness

The `"suspensionStiffness"` property is a number that defines the stiffness of the suspension, the resistance to moving up or down.

### Suspension Travel

The `"suspensionTravel"` property is a number that defines the distance in meters the wheel can move up or down.

### Use For Steering

The `"useForSteering"` property is a boolean that specifies if the wheel should rotate when the vehicle desires to steer. If not specified, the wheel should be used for steering.

What causes steering to occur is implementation-defined. The steering may occur when the pilot tries to steer the vehicle, such as a user pressing buttons, or an NPC controlling the vehicle.

### Use For Traction

The `"useForTraction"` property is a boolean that specifies if this wheel should be used to transfer the engine force to the ground to propel the vehicle in its desired direction. If not specified, the wheel should be used for traction.

### JSON Schema

See [wheel.schema.json](schema/wheel.schema.json) for the main wheel parameter schema, [glTF.OMI_vehicle_wheel.schema.json](schema/glTF.OMI_vehicle_wheel.schema.json) for the document-level list of wheel parameters, and [node.OMI_vehicle_wheel.schema.json](schema/node.OMI_vehicle_wheel.schema.json) for the node-level collider selection.

## Known Implementations

* Godot Engine: https://github.com/godotengine/godot/pull/69266
* Third Room Unity Exporter: https://github.com/matrix-org/thirdroom-unity-exporter/blob/main/Runtime/Scripts/OMI_collider/OMI_ColliderExtension.cs
* Third Room glTF Transform: https://github.com/matrix-org/thirdroom/blob/main/src/asset-pipeline/extensions/OMIColliderExtension.ts
* Third Room glTF Loader: https://github.com/matrix-org/thirdroom/blob/main/src/engine/gltf/OMI_collider.ts
* Three Object Viewer WordPress Plugin: https://github.com/antpb/three-object-viewer/

## Resources:

* Godot Shapes: https://docs.godotengine.org/en/latest/classes/class_shape3d.html
* Unity Colliders: https://docs.unity3d.com/Manual/CollidersOverview.html
* Unreal Engine Collision Shapes: https://docs.unrealengine.com/4.27/en-US/API/Runtime/PhysicsCore/FCollisionShape/
* Unreal Engine Mesh Collisions: https://docs.unrealengine.com/4.27/en-US/WorkingWithContent/Types/StaticMeshes/HowTo/SettingCollision/
* Blender Collisions: https://docs.blender.org/manual/en/latest/physics/rigid_body/properties/collisions.html
* Mozilla Hubs ammo-shape: https://github.com/MozillaReality/hubs-blender-exporter/blob/bb28096159e1049b6b80da00b1ae1534a6ca0855/default-config.json#L608
