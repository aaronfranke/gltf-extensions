# OMI_vehicle_body

## Contributors

- Aaron Franke, Godot Engine.

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

|                          | Type        | Description                                                              | Default value   |
| ------------------------ | ----------- | ------------------------------------------------------------------------ | --------------- |
| **angularActivation**    | `number[3]` | The input value controlling the ratio of the vehicle's angular forces.   | [0.0, 0.0, 0.0] |
| **linearActivation**     | `number[3]` | The input value controlling the ratio of the vehicle's linear forces.    | [0.0, 0.0, 0.0] |
| **pilotSeat**            | `number`    | The index of the `OMI_seat` glTF node to use as the pilot / driver seat. | -1 (no seat)    |
| **maximumSpeed**         | `number`    | The speed at which the vehicle should stop driving acceleration further. | -1.0            |
| **maximumSteeringAngle** | `number`    | The maximum angle in radians that the vehicle can steer.                 | 0.5             |
| **steeringAngle**        | `number`    | The current angle in radians that the vehicle is steering.               | 0.0             |
| **throttleRatio**        | `number`    | The ratio of throttle to apply to the vehicle, if useThrottle is true.   | 0.0             |
| **useThrottle**          | `boolean`   | If true, the vehicle should use a throttle for forward movement.         | false           |
| **inertiaDampeners**     | `boolean`   | If true, the vehicle should slow itself down when not given input.       | false           |

#### Angular Activation

The `"angularActivation"` property is an array of three numbers that defines the input value controlling the ratio of the vehicle's angular forces. If not specified, the default value is [0.0, 0.0, 0.0], which means that the vehicle should not drive itself with angular forces.

The three numbers represent the ratio of activation of the vehicle's angular forces around the X, Y, and Z axes, respectively. The values are expected to be between -1.0 and 1.0. The behavior of values outside of this range is implementation-defined, they may be clamped to this range, or be allowed to go beyond this range for some kind of turbo boost or overdrive.

For example, a value of [0.0, 1.0, 0.0] would mean that the vehicle should try to spin around its local Y axis to the left at full speed. For a car, this would mean wheels used for steering are turned to the left. For an aircraft or sea ship, this would mean turning the rudder so that the vehicle rotates to the left. For a spacecraft, this may activate gyroscopes or small RCS thrusters to rotate the vehicle to the left. If an angular direction is not applicable to the vehicle, the value should be ignored, such as a car ignoring X and Z angular activations since it can only steer left and right.

Note that due to glTF's right-handed coordinate system with +Z as forward, positive X rotations rotate down, positive Y rotations rotate to the left, and positive Z rotations rotate clockwise.

This value is expected to dynamically change at runtime. This is not usually saved in the glTF file, but it is allowed to be. The input source for angular activation is implementation-defined. The input may be from a pilot, such as a user pressing buttons, an NPC controlling the vehicle, or a script such as `KHR_interactivity`.

#### Linear Activation

The `"linearActivation"` property is an array of three numbers that defines the input value controlling the ratio of the vehicle's linear forces. If not specified, the default value is [0.0, 0.0, 0.0], which means that the vehicle should not drive itself with linear forces.

The three numbers represent the ratio of activation of the vehicle's linear forces in the X, Y, and Z directions, respectively. The values are expected to be between -1.0 and 1.0. The behavior of values outside of this range is implementation-defined, they may be clamped to this range, or be allowed to go beyond this range for some kind of turbo boost or overdrive.

For example, a value of [0.0, 0.0, 1.0] would mean that the vehicle should propel itself forward on its local +Z axis at full speed. For a car, this would mean driving the wheels forward with maximum force. For an aircraft or sea ship, this would mean activating main propellers or turbines at full power. For a spaceship, this would mean firing the rear thrusters at full power. If a linear direction is not applicable to the vehicle, the value should be ignored, such as a car ignoring X and Y linear activations since it can only drive forward and backward.

This value is expected to dynamically change at runtime. This is not usually saved in the glTF file, but it is allowed to be. The input source for linear activation is implementation-defined. The input may be from a pilot, such as a user pressing buttons, an NPC controlling the vehicle, or a script such as `KHR_interactivity`.

#### Pilot Seat

The `"pilotSeat"` property is an integer that defines the index of the glTF node to use as a pilot seat, also known as driver's seat in the context of cars. The node that `"pilotSeat"` points to should be a seat as defined by the `OMI_seat` extension. If not specified or set to -1, this vehicle does not have a pilot seat.

A character sitting in the pilot seat is called the pilot. The pilot should be given control of the vehicle through whatever control scheme the implementation desires. The pilot seat is expected to face +Z to have the pilot facing towards the front of the vehicle. If an implementation does not allow for characters to pilot the vehicle, this property can be ignored.

The pilot seat node is usually a child of the vehicle body node, but it does not have to be. A pilot seat node that is not a descendant of the vehicle body allows for a remote-controlled vehicle, such as a drone.

#### Maximum Speed

The `"maximumSpeed"` property is a number that defines the speed in meters per second at which the vehicle should stop driving acceleration further in that direction. If not defined or negative, the vehicle should not have a maximum speed.

This only affects the maximum speed at which the vehicle can accelerate to under its own power, in all directions. If the vehicle is already moving faster than this speed, the vehicle does not need to use its own thrust to slow down, but it is allowed to do so if given input to thrust in the opposite direction, or if inertia dampeners are enabled.

Note that this only affects how the vehicle is driven, not how it is simulated. The physics engine should still simulate the vehicle at any speed. For example, if a vehicle is driven off a cliff, it may accelerate due to gravity, unrelated to the maximum speed.

#### Maximum Steering Angle

The `"maximumSteeringAngle"` property is a number that defines the maximum angle in radians that the vehicle can steer. If not specified, the default value is 0.5 radians, which is about 28.64788975654116... degrees, which is a sane default that reflects real-world cars. For more details on steering, see "Steering Angle" below.

#### Steering Angle

The `"steeringAngle"` property is a number that defines the current angle in radians that the vehicle is steering in. If not specified, the default value is 0.0 radians, which means the vehicle is steering straight ahead.

The exact interpretation of this value depends on the type of vehicle. For cars, this value represents the rotation of the front wheels around their local Y axis. For aircraft, this may control the landing gear wheels. For sea ships, this value may be used to control the rudder. For spacecraft, this value usually does nothing. It is up to implementations and other extensions to define the exact behavior of this value. When this value does apply, due to glTF's right-handed coordinate system, positive values steer to the left, and negative values steer to the right.

The steering angle should be kept between -`"maximumSteeringAngle"` and `"maximumSteeringAngle"`. The behavior of values outside of this range are implementation-defined. Depending on the desired behavior, the steering angle may be clamped to this range, or be allowed to go outside of this range for some kind of drift or oversteer.

This property is expected to be dynamically changed at runtime, such as by a pilot or by script. What causes steering to occur is implementation-defined. The steering may occur when the pilot tries to steer the vehicle, such as a user pressing buttons, or an NPC controlling the vehicle. The rate of steering change and the control scheme are implementation-defined.

#### Throttle Ratio

The `"throttleRatio"` property is a number that defines the ratio of the throttle to apply to the vehicle. If not specified, the default value is 0.0, meaning that if throttle is enabled, the vehicle should be at 0% forward thrust.

Throttle is a common feature of airplanes and boats, but may be used on other kinds of vehicles. The throttle is usually between 0.0 and 1.0, where 0.0 is no throttle and 1.0 is full throttle. The behavior of values outside of this range are implementation-defined. Depending on the desired behavior, negative throttle may be used for reverse, or throttle values above 1.0 may be used for some kind of boost or overdrive, or the throttle may be clamped to the range of 0.0 to 1.0. Throttle is expected to be dynamically changed at runtime, such as by a pilot or by script.

#### Use Throttle

The `"useThrottle"` property is a boolean that defines whether the vehicle should use a throttle for forward movement.

For example, consider the case where the vehicle is given input to move forward, such as a player pressing the <kbd>W</kbd> key, or any other input method.
- Without a throttle, the vehicle should accelerate as fast as it can in the forward direction, up to the maximum speed; then when <kbd>W</kbd> is released, the vehicle should stop accelerating forward, either continuing at the same speed, or if inertia dampeners are enabled, slowing down.
- With a throttle, the <kbd>W</kbd> key should increase the throttle, and the <kbd>S</kbd> key should decrease the throttle, allowing the player to control how fast the vehicle accelerates; when the keys are no longer pressed, the throttle stays at its value. The rate of throttle change and the control scheme are implementation-defined.

#### Inertia Dampeners

The `"inertiaDampeners"` property is a boolean that defines whether the vehicle should slow itself down when not given input.

When inertia dampeners are enabled, it indicates that the vehicle should attempt to come to a stop. This may be the brakes on a car, the reverse thrusters on a spaceship, or some other mechanism. For example, a spaceship with inertia dampeners should use its thrusters to slow down and stop moving when the pilot stops giving input, rather than continuing to drift in space forever.

The reference frame used to determine what "stop" and "not moving" mean is implementation-defined, and usually determined at runtime. Inertia dampeners may be relative to global coordinates, or relative to some object, such as a planet, moon, space station, or another vehicle.

### JSON Schema

See [schema/node.OMI_physics_body.schema.json](schema/node.OMI_physics_body.schema.json).

## Known Implementations

* Godot Engine: https://github.com/omigroup/omi-godot/pull/2

## Resources:

* None so far.
