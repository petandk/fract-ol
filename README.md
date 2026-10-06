*This project has been created as part of the 42 curriculum by rmanzana*

# fract-ol

## Description

**fract-ol** is a fractal explorer written in C with the **MiniLibX**, the small graphics library used at 42. It renders the **Mandelbrot** and **Julia** sets in a window and lets you explore them in real time by zooming with the mouse and moving with the arrow keys.

The project is an introduction to computer graphics (drawing pixels into an image buffer, handling window events) and to the maths behind fractals: complex numbers and iterative sequences.

### How the fractals are computed

Both fractals come from the same iteration on complex numbers:

$$z_{n+1} = z_n^2 + c$$

A point belongs to the set if the sequence stays bounded; it escapes as soon as $|z| > 2$. Each pixel of the window is mapped to a point of the complex plane, the sequence is iterated up to `MAX_ITERATIONS` (100) times, and the number of iterations before escaping decides the pixel colour.

- **Mandelbrot:** $z_0 = 0$ and $c$ is the pixel's position.
- **Julia:** $z_0$ is the pixel's position and $c$ is a constant given as a parameter. Every constant produces a different fractal.

### Optimisations

- **Cardioid and bulb check:** points inside the Mandelbrot main cardioid or the period-2 bulb are known to belong to the set, so they are detected with a direct formula and skip the iteration loop. These are the most expensive points to compute, since they always reach `MAX_ITERATIONS`.
- **Periodicity check:** every 10 iterations, if $z$ has returned to a previous value, the sequence is in a cycle and will never escape, so the loop stops early.
- **Precomputed palette:** the colour for each iteration count is computed once at start-up and stored in an array, so drawing a pixel is a table lookup.
- **Off-screen image buffer:** pixels are written directly into an MLX image in memory and the whole frame is pushed to the window at once, instead of drawing pixel by pixel on screen.
- Compiled with `-Ofast -ffast-math -march=native`.

## Instructions

### Requirements

The Linux version of the MiniLibX is **included** in `minilibx-linux/` and is compiled automatically by the Makefile; nothing has to be downloaded.

The MiniLibX is built on top of X11, so the X11 development headers must be present on the system. They are already installed on the 42 computers; on a fresh Debian / Ubuntu machine:

```bash
sudo apt install libx11-dev libxext-dev
```

### Compilation

```bash
make          # builds the MiniLibX and the fractol executable
make clean    # removes object files
make fclean   # removes object files and the executable
make re       # rebuilds everything
```

### Usage

```bash
./fractol mandelbrot
./fractol julia <real> <imaginary>
```

The Julia parameters must be numbers between `-2` and `2`. Running `./fractol` with no arguments prints a help message with some interesting constants to try:

```bash
./fractol julia -0.4 0.6
./fractol julia 0.285 0.01
./fractol julia -0.8 0.156
./fractol julia -0.7269 0.1889
```

### Controls

| Input | Action |
|---|---|
| Mouse wheel | Zoom in / out, following the mouse position |
| Arrow keys | Move the view |
| `R` | Reset the view |
| `ESC` or closing the window | Quit the program cleanly |

## Resources

- [Mandelbrot set — Wikipedia](https://en.wikipedia.org/wiki/Mandelbrot_set) (including the cardioid / bulb checking and periodicity checking optimisations)
- [Julia set — Wikipedia](https://en.wikipedia.org/wiki/Julia_set)
- [The Mandelbrot Set — Numberphile](https://www.youtube.com/watch?v=NGMRB4O922I)
- [MiniLibX documentation — harm-smits](https://harm-smits.github.io/42docs/libs/minilibx)
- [Plotting algorithms for the Mandelbrot set — Wikipedia](https://en.wikipedia.org/wiki/Plotting_algorithms_for_the_Mandelbrot_set)
