# GxJSON

**GxJSON (Geometry/GIS exchange)** is a versatile JSON-based format for representing geometric and GIS data.

## Key Features

- **Structured JSON Format**: Uses a clear and consistent JSON structure to represent geometric shapes and GIS data.
- **Extensibility**: Supports optional fields and metadata for customization.
- **Multi-Dimensional Support**: Handles 2D, 3D, and 4D geometries.
- **Attributes**: Supports additional attributes for polygons, including texture coordinates, surface normals, and colors.

## Specification

### Common Fields (All Geometries)

| Field  | Type            | Required | Description                                  |
|--------|-----------------|----------|---------------------------------------------|
| `f`    | Unsigned Integer| Yes      | Form of the geometry. 1 = Point, 2 = Polyline, 3 = Polygon. |
| `d`    | Unsigned Integer| Yes      | Number of dimensions. 2 = 2D, 3 = 3D, 4 = 4D. |
| `meta` | JSON Object     | No       | Optional metadata. Must be a flat key-value pair object (no nested objects). |

### Geometry Types

#### 1. Point
Represents a single location in space.

| Field | Type   | Required | Description                                  |
|-------|--------|----------|---------------------------------------------|
| `u`   | Number | Yes      | Horizontal coordinate (e.g., x or longitude). |
| `v`   | Number | Yes      | Vertical coordinate (e.g., y or latitude).   |
| `w`   | Number | No       | Depth (e.g., z or altitude). Required if `d ≥ 3`. |
| `t`   | Number | No       | 4th component (e.g., time or another dimension). Required if `d = 4`. |

**Examples:**

**2D Point:**
```json
{
  "f": 1,
  "d": 2,
  "u": 69.12,
  "v": 41.12
}
```

**3D Point:**
```json
{
  "f": 1,
  "d": 3,
  "u": 69.12,
  "v": 41.12,
  "w": 1.55
}
```

**4D Point:**
```json
{
  "f": 1,
  "d": 4,
  "u": 69.12,
  "v": 41.12,
  "w": 0.05,
  "t": 1.0
}
```

#### 2. Polyline
Represents a sequence of connected points.

| Field       | Type            | Required | Description                                  |
|-------------|-----------------|----------|---------------------------------------------|
| `positions` | Array of Numbers | Yes      | Flattened array of coordinates (e.g., [u1, v1, u2, v2, ...]). |

**Example:**
**2D Polyline:**
```json
{
  "f": 2,
  "d": 2,
  "positions": [ 69.12,41.12, 70.0,42.0, 71.0,43.0 ]
}
```

#### 3. Polygon
Represents a closed shape defined by a sequence of points. Supports holes and additional attributes like normals, texture coordinates, and colors.

| Field      | Type            | Required | Description                                  |
|------------|-----------------|----------|---------------------------------------------|
| `positions`| Array of Numbers | Yes      | Flattened array of coordinates (e.g., [u1, v1, u2, v2, ...]). |
| `rings`    | Array of Integers| Yes      | Defines the start and end of rings (e.g., for polygons with holes). |
| `indices`  | Array of Integers| No       | Triangle indices for rendering.             |
| `normals`  | Array of Numbers | No       | Surface normals (e.g., [nx1, ny1, nz1, ...]). |
| `uvs2i`    | Array of Integers| No       | 2D texture coordinates (integer).           |
| `uvs2f`    | Array of Numbers | No       | 2D texture coordinates (float).             |
| `uvs3i`    | Array of Integers| No       | 3D texture coordinates (integer).           |
| `uvs3f`    | Array of Numbers | No       | 3D texture coordinates (float).             |
| `colors3i` | Array of Integers| No       | RGB colors (integer, range 0–255).          |
| `colors4i` | Array of Integers| No       | RGBA colors (integer, range 0–255).         |
| `colors3f` | Array of Numbers | No       | RGB colors (float, range 0–1).              |
| `colors4f` | Array of Numbers | No       | RGBA colors (float, range 0–1).             |

**Example:**
**2D Polygon:**
```json
{
  "f": 3,
  "d": 2,
  "positions": [ 69.12,41.12, 70.0,42.0, 71.0,43.0 ],
  "rings": [ 0, 3 ],
  "indices": [ 0,1,2 ],
  "normals": [ 0,1,0, 0,1,0, 0,1,0 ],
  "uvs2f": [ 0,0, 1,0, 1,1 ],
  "colors3f": [ 1,0,0, 0,1,0, 0,0,1 ]
}
```

## Extensibility

The `meta` field allows for the inclusion of additional metadata. For example:

```json
{
  "f": 1,
  "d": 2,
  "u": 69.12,
  "v": 41.12,
  "meta": {
    "name": "Sample Point",
    "description": "A sample 2D point with metadata."
  }
}
```

## Notes

- **Validation**: Ensure that the number of coordinates in `positions` matches the dimensionality (`d`).
- **Polygon**: A polygon is logically closed, but it is not required for the first and last points in the array to be identical.
