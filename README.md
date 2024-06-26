# SimpleSim
Hello! This environment is a simple simulation engine to view and control a standard VEX Clawbot and move it via motor torques.

### Example
You will notice that the interface to the simulation engine is very similar to Gym's interface, as Gym seems to be the most popular RL sim framework, and for good reason. It's quite simple to read, and easy to get up and running.

```python
from sim3d import TennisBallClawbotEnv

if __name__ == "__main__":
  env = TennisBallClawbotEnv()

  while True:
    obs = env.reset()
    for step in range(200):
      action = [0] * 10
      action[0] = 0.5 # left
      action[1] = -0.5 # right

      new_obs, reward, term, info = env.step(motors)
      env.render()
```

### Description
A ball is positioned randomly inside of a standard 144"x144" field. The goal of the robot is to navigate to the position of the ball such that the ball is at the center of the claw. This environment via Three.js using Ammo/Bullet physics. It runs on Windows/Mac/Linux and does not require a GPU.

|  |  |
| -- | -- |
| Action Space | Box(-1.0, 1.0, (10,), float32) |
| Observation Shape | (10,) |
| Observation High | [inf inf inf inf inf inf inf inf inf inf ] |
| Observation Low | [-inf -inf -inf -inf -inf -inf -inf -inf -inf -inf ] |
| Import | `from sim3d import TennisBallClawbotEnv` |

### Action Space
Some actions are left intentionally blank to reflect a VEX microcontrollers disconnected ports.

| Num | Action | Control Min | Control Max | Unit |
| --- | ------ | ----------- | ----------- | ---- |
| 0 | Angular velocity target of the left motor | -1 | 1 | rpm (rad/m) |
| 1 | Torque applied on the arm | -1 | 1 | Torque (Nm) |
| 2 | Torque applued on the claw (<i>pending</i>) | -1 | 1 | Torque (Nm) |
| 3 | ❌ |  |  |  |
| 4 | ❌ |  |  |  |
| 5 | ❌ |  |  |  |
| 6 | ❌ |  |  |  |
| 7 | ❌ |  |  |  |
| 8 | ❌ |  |  |  |
| 9 | Angular velocity target of the right motor | -1 | 1 | rpm (rad/m) |

### Observation Space

| Num | Action | Min | Max | Unit |
| --- | ------ | --- | --- | ---- |
| 0 | Chassis position x | -1.83 | 1.83 | position (m) |
| 1 | Chassis position y | -1.83 | 1.83 | position (m) |
| 2 | Counter-clockwise heading of chassis, y-axis=0 | -𝜋 | 𝜋 | radians |
| 3 | Pitch of the arm | -𝜋/2 | 𝜋/2 | radians |
| 4 | Arm angular velocity | -inf | inf | rad/s |
| 5 | Ball position x | -1.83 | 1.83 | position (m) |
| 6 | Ball position y | -1.83 | 1.83 | position (m) |
| 7 | Ball distance from claw | 0 | 13.38 | distance (m) |
| 8 | Counter-clockwise heading relative to ball | -𝜋 | 𝜋 | radians |
| 9 | Pitch relative to ball | 0 | 0 | radians |

### Reward
A reward is given based on the distance the claw is from the ball, as well as the difference in orientation of the claw from the direction of the claw to the target ball.

`reward = 1 - sum(ball.xyz - claw.xyz).sqrt() - abs(heading - angleto(claw, ball)) / PI`