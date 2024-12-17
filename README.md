# LiDAR Messages

## LaserScan

The `LaserScanRaw` message represents the raw data captured by a 2D LiDAR sensor. Below is a detailed description of its fields:

| **Field**           | **Type**                      | **Tag** | **Description**                                                                                      |
|----------------------|-------------------------------|---------|------------------------------------------------------------------------------------------------------|
| `timestamp`         | `google.protobuf.Timestamp`   | 1       | Timestamp indicating when the scan was captured.                                                    |
| `angle_min`         | `float`                      | 2       | Starting angle of the scan in radians. Typically `0.0` for a full 360-degree LiDAR.                 |
| `angle_max`         | `float`                      | 3       | Ending angle of the scan in radians. Typically `2π` for a full 360-degree LiDAR.                   |
| `angle_increment`   | `float`                      | 4       | Angular resolution of the scan in radians per step.                                                 |
| `range_min`         | `float`                      | 5       | Minimum measurable range of the LiDAR in meters.                                                    |
| `range_max`         | `float`                      | 6       | Maximum measurable range of the LiDAR in meters.                                                    |
| `ranges`            | `repeated float`             | 7       | Array of measured distances in meters for each step in the scan.                                    |
| `intensities`       | `repeated float` (optional)  | 8       | Array of signal intensity values (unitless) corresponding to the measured ranges.                   |
| `checksum`          | `uint32` (optional)          | 9       | Checksum value for data integrity verification.                                                     |

<details>
<summary> Read more </summary>

### `timestamp`
- Captures the time when the scan data was recorded. Useful for synchronizing data with other sensors.

### `angle_min` & `angle_max`
- Represent the angular limits of the scan:
  - `angle_min = 0.0`: The scan starts at 0 radians.
  - `angle_max = 6.28319`: The scan ends at `2π` radians.

### `angle_increment`
- Defines the angular resolution (spacing) between consecutive measurements. 
- Example: An angular increment of `0.01745` radians corresponds to a 1-degree step.

### `range_min` & `range_max`
- Define the valid distance range for the LiDAR sensor. Measurements outside this range are considered invalid.

### `ranges`
- Contains the measured distances for each angular step. 
- The array size is calculated as:

Number of Steps = (angle_max - angle_min) / angle_increment

### `intensities`
- (Optional) Reflectance values corresponding to each measured range. Higher values indicate stronger reflections.

### `checksum`
- (Optional) Ensures the integrity of the transmitted data. Typically calculated using a CRC or similar algorithm.

## Example Usage

Here’s an example of a serialized `LaserScanRaw` message in JSON format:

```json
{
"timestamp": "2024-12-16T10:45:00Z",
"angle_min": 0.0,
"angle_max": 6.28319,
"angle_increment": 0.01745,
"range_min": 0.15,
"range_max": 12.0,
"ranges": [1.0, 1.05, 1.1, 1.2, 1.15],
"intensities": [150, 145, 140, 135, 130],
"checksum": 12345
}
```

### `to get (x,y) vlaue for a point

convert from polar to cartesion co-ordinates

    ```
        x = range * cos(angle)
        y = range * sin(angle)
    ```

</details>

---

## PointCloud

The `PointCloudRaw` message represents the raw data captured by a 3D LiDAR sensor. Below is a detailed description of its fields:

| **Field**       | **Type**                    | **Tag** | **Description**                                                                                   |
|------------------|-----------------------------|---------|---------------------------------------------------------------------------------------------------|
| `timestamp`     | `google.protobuf.Timestamp` | 1       | Timestamp indicating when the point cloud packet was received.                                   |
| `header`        | `uint32`                   | 2       | Header field identifying the start of the packet.                                                |
| `points`        | `repeated Point`           | 3       | Array of points in the point cloud. Each point contains 3D coordinates and optional metadata.    |
| `frame_id`      | `string`                   | 4       | Frame ID to associate the point cloud with a specific coordinate frame (e.g., `lidar_frame`).   |
| `checksum`      | `uint32`                   | 5       | Checksum value for verifying the integrity of the received data.                                 |

---

## Point Message (Nested)

The `Point` message represents a single point in the point cloud. Below is a detailed description of its fields:

| **Field**         | **Type**     | **Tag** | **Description**                                                                                 |
|--------------------|--------------|---------|-------------------------------------------------------------------------------------------------|
| `x`               | `float`     | 1       | The x-coordinate of the point in meters.                                                       |
| `y`               | `float`     | 2       | The y-coordinate of the point in meters.                                                       |
| `z`               | `float`     | 3       | The z-coordinate of the point in meters.                                                       |
| `intensity`       | `float`     | 4       | (Optional) Intensity of the reflected signal from the point (unitless).                        |
| `classification`  | `uint32`    | 5       | (Optional) Additional classification or quality information for the point.                     |

---

<details>
<summary> Read More </summary>

### `timestamp`
- Captures the time the data was received.
- Useful for aligning data from multiple sensors or frames in time-sensitive applications.

### `header`
- Identifies the start of a packet.
- Used by the receiver to validate that the incoming data is a valid point cloud packet.

### `points`
- Represents the collection of 3D points in the point cloud.
- Each `Point` contains detailed information such as position (`x`, `y`, `z`), reflectivity (`intensity`), and optional classification data.

### `frame_id`
- Provides a reference frame for the point cloud.
- Used in applications where the point cloud data must be transformed or aligned to a common frame (e.g., robot-relative or world-relative).

### `checksum`
- Ensures data integrity by verifying that the packet was not corrupted during transmission.

### `x`, `y`, `z`
- Cartesian coordinates representing the position of a point in 3D space.
- Measured in meters and define the spatial location of the point.

### `intensity`
- Optional field that captures the reflectance strength of the LiDAR return signal.
- Higher values indicate better reflectivity, useful for distinguishing between materials.

### `classification`
- Optional field to tag points with additional semantic information.
- Example: 1 = ground, 2 = obstacle, 3 = vegetation.

---

## Example Usage

Below is an example JSON representation of a `PointCloudRaw` message:

```json
{
  "timestamp": "2024-12-16T10:45:00Z",
  "header": 12345,
  "points": [
    {"x": 1.0, "y": 2.0, "z": 0.5, "intensity": 150.0, "classification": 1},
    {"x": 1.5, "y": 2.5, "z": 0.6, "intensity": 145.0, "classification": 2},
    {"x": 1.8, "y": 2.8, "z": 0.7, "intensity": 140.0, "classification": 0}
  ],
  "frame_id": "lidar_frame",
  "checksum": 67890
}
```
</details>

---