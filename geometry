import tkinter as tk
import random
import colorsys

WIDTH = 800
HEIGHT = 400

root = tk.Tk()
root.title("Wave Mode - Clean Trail")
canvas = tk.Canvas(root, width=WIDTH, height=HEIGHT, bg="black")
canvas.pack()

# ---------- PLAYER ----------
player = canvas.create_polygon(80, 200, 80, 230, 110, 215, fill="cyan")

velocity_y = 0
gravity = 0.6
wave_power = -1.3
holding = False

obstacles = []
trail = []
score = 0
game_over = False

score_text = canvas.create_text(80, 30, text="Score: 0", fill="white", font=("Arial", 18))


# ---------- INPUT ----------
def key_down(event):
    global holding
    if event.keysym == "space":
        holding = True


def key_up(event):
    global holding
    if event.keysym == "space":
        holding = False


# ---------- RAINBOW ----------
hue = 0

def rainbow():
    global hue
    hue += 0.01
    if hue > 1:
        hue = 0
    r, g, b = colorsys.hsv_to_rgb(hue, 1, 1)
    return f"#{int(r*255):02x}{int(g*255):02x}{int(b*255):02x}"


# ---------- OBSTACLES ----------
def create_obstacle():
    gap_y = random.randint(120, 280)
    gap_size = max(90, 140 - score // 10)

    top = canvas.create_rectangle(
        WIDTH, 0,
        WIDTH + 40,
        gap_y - gap_size // 2,
        fill="red"
    )

    bottom = canvas.create_rectangle(
        WIDTH,
        gap_y + gap_size // 2,
        WIDTH + 40,
        HEIGHT,
        fill="red"
    )

    obstacles.append((top, bottom))


# ---------- COLLISION ----------
def check_collision(a, b):
    ax1, ay1, ax2, ay2 = canvas.bbox(a)
    bx1, by1, bx2, by2 = canvas.bbox(b)

    margin = 8
    bx1 += margin
    bx2 -= margin
    by1 += margin
    by2 -= margin

    return not (ax2 < bx1 or ax1 > bx2 or ay2 < by1 or ay1 > by2)


# ---------- TRAIL (FIXED) ----------
def add_trail(x, y):
    color = rainbow()
    dot = canvas.create_oval(x, y, x+5, y+5, fill=color, outline="")
    trail.append(dot)


def update_trail():
    # trail STAYS in place, only fades out
    for t in trail[:]:
        if random.randint(1, 4) == 1:
            canvas.delete(t)
            trail.remove(t)


# ---------- SCREEN SHAKE ----------
def shake():
    for _ in range(10):
        canvas.move("all", random.randint(-5, 5), random.randint(-5, 5))
        canvas.update()


# ---------- PLAYER ----------
def move_player():
    global velocity_y

    if holding:
        velocity_y += wave_power
    else:
        velocity_y += gravity

    velocity_y = max(-6, min(6, velocity_y))

    canvas.move(player, 0, velocity_y)

    x1, y1, x2, y2, x3, y3 = canvas.coords(player)

    cx = (x1 + x2 + x3) / 3
    cy = (y1 + y2 + y3) / 3

    add_trail(cx, cy)

    # bounds
    if cy < 0:
        canvas.move(player, 0, -cy)
        velocity_y = 0

    if cy > HEIGHT:
        canvas.move(player, 0, HEIGHT - cy)
        velocity_y = 0


# ---------- OBSTACLES ----------
def move_obstacles():
    global score, game_over

    speed = 10 + score // 10

    for pair in obstacles[:]:
        top, bottom = pair

        canvas.move(top, -speed, 0)
        canvas.move(bottom, -speed, 0)

        if canvas.bbox(top)[2] < 0:
            canvas.delete(top)
            canvas.delete(bottom)
            obstacles.remove(pair)

            score += 1
            canvas.itemconfig(score_text, text=f"Score: {score}")
            continue

        if check_collision(player, top) or check_collision(player, bottom):
            game_over = True
            shake()
            canvas.create_text(
                WIDTH//2, HEIGHT//2,
                text="GAME OVER\nPress R",
                fill="white",
                font=("Arial", 30)
            )


# ---------- LOOP ----------
def game_loop():
    if not game_over:
        move_player()
        move_obstacles()
        update_trail()

        if random.randint(1, max(25, 45 - score)) == 1:
            create_obstacle()

    root.after(20, game_loop)


# ---------- RESTART ----------
def restart(event=None):
    global player, obstacles, trail, velocity_y, score, game_over, holding

    canvas.delete("all")

    global score_text

    player = canvas.create_polygon(80, 200, 80, 230, 110, 215, fill="cyan")

    obstacles.clear()
    trail.clear()

    velocity_y = 0
    score = 0
    game_over = False
    holding = False

    score_text = canvas.create_text(80, 30, text="Score: 0", fill="white", font=("Arial", 18))


# ---------- CONTROLS ----------
root.bind("<KeyPress>", key_down)
root.bind("<KeyRelease>", key_up)
root.bind("r", restart)

game_loop()
root.mainloop()
