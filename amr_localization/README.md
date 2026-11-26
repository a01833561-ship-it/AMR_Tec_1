# `amr_localization`

Owns state estimation and global localization configuration. It fuses wheel odometry, IMU, GNSS, and SLAM/localization outputs into a documented TF chain suitable for navigation.

| Directory | Add |
| --- | --- |
| `config/` | EKF/UKF, GNSS, map-localization, and sensor-fusion parameters |
| `launch/` | Localization and state-estimation launch files |
| `maps/` | Curated static map assets and metadata used by localization |

Document the authority for each transform. The expected navigation chain is `map -> odom -> base_link`; only one component may publish each transform. Keep covariance, timestamp, and sensor-frame configuration explicit in YAML.


1. **Feather firmware** reads the IMU (turn rate, acceleration) and the encoder (wheel speed). It prints one line of numbers 20 times per second over USB.
2. **`sensor_node.py`** reads those lines and publishes them as ROS topics: `/imu/data` and `/wheel/odom`.
3. **`ekf.yaml`** tells the EKF filter to combine wheel speed, IMU turn rate and IMU acceleration into one smoother estimate. The wheel speed alone is noisy, and the IMU fills the gaps.
4. **The launch file** starts all of this with one command.

**Output:** `/odometry/filtered` gives the vehicle's forward speed and turning speed. Later, the bigger autonomous system uses this topic to know how the vehicle is moving.

**The IMU's role:** the gyro says how fast the vehicle is turning, which the encoder can't measure. The acceleration helps the filter notice speed changes faster than the wheel alone.
