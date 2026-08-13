import numpy as np
import matplotlib.pyplot as plt
from matplotlib.animation import FuncAnimation
from matplotlib.patches import Rectangle, Circle
from matplotlib.transforms import Affine2D

# Figure
fig, ax = plt.subplots(figsize=(7,7))

ax.set_xlim(-5,5)
ax.set_ylim(-5,5)
ax.set_aspect('equal')
ax.grid(True)

ax.set_title("Differential Drive Robot - Forward Kinematics")

# Robot body
robot = Rectangle((-0.3,-0.2), 0.6, 0.4,
                  facecolor="skyblue",
                  edgecolor="black")

ax.add_patch(robot)

# Wheels
wheel_R = Circle((0.25,0),0.08,color="black")
wheel_L = Circle((-0.25,0),0.08,color="black")

ax.add_patch(wheel_R)
ax.add_patch(wheel_L)


# Initial pose
x = 0
y = 0
theta = 0

# Robot parameters
r = 0.05       # wheel radius
L = 0.4        # distance between wheels

# Wheel velocities
w_R = 4        # right wheel angular velocity
w_L = 2        # left wheel angular velocity

dt = 0.05


# Path
path_x = []
path_y = []

trajectory, = ax.plot([],[], 'r')


def update(frame):
    global x, y, theta

    # Forward Kinematics
    v = (r/2)*(w_R + w_L)
    omega = (r/L)*(w_R - w_L)

    # Robot position update
    x += v*np.cos(theta)*dt
    y += v*np.sin(theta)*dt
    theta += omega*dt


    path_x.append(x)
    path_y.append(y)

    trajectory.set_data(path_x,path_y)


    # Move robot
    transform = (
        Affine2D()
        .rotate(theta)
        .translate(x,y)
        + ax.transData
    )

    robot.set_transform(transform)


    # Move wheels with robot
    wheel_R.set_center((x+0.25*np.cos(theta),
                        y+0.25*np.sin(theta)))

    wheel_L.set_center((x-0.25*np.cos(theta),
                        y-0.25*np.sin(theta)))

    return robot, trajectory, wheel_R, wheel_L



animation = FuncAnimation(
    fig,
    update,
    frames=300,
    interval=50,
    blit=False
)


plt.show()
