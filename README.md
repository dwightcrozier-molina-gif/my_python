import tkinter as tk

root = tk.Tk()
root.title("Fullscreen Real Keyboard")
root.attributes("-fullscreen", True)

text = tk.Text(root, height=5, font=("Arial", 18))
text.pack(fill="x")

status = tk.Label(root, text="", font=("Arial", 16), fg="red")
status.pack()

shift = False

keyboard_frame = tk.Frame(root)
keyboard_frame.pack(expand=True)

# center everything like a real keyboard
keyboard_frame.grid_columnconfigure(0, weight=1)

layout = [
    ["Esc","1","2","3","4","5","6","7","8","9","0","Backspace"],
    ["Tab","Q","W","E","R","T","Y","U","I","O","P"],
    ["Caps","A","S","D","F","G","H","J","K","L","Enter"],
    ["Shift","Z","X","C","V","B","N","M","," ,".","/","Shift"],
    ["Ctrl","Win","Alt","Space","Alt","Win","Menu","Ctrl"]
]

buttons = []

def press(key):
    global shift

    if key == "Shift":
        shift = not shift
        return

    if key == "Space":
        text.insert("insert", " ")
        return

    if key == "Enter":
        text.insert("insert", "\n")
        return

    if key == "Tab":
        text.insert("insert", "\t")
        return

    if key == "Backspace":
        if text.index("insert") != "1.0":
            text.delete("insert-1c")
        return

    if len(key) == 1:
        key = key.upper() if shift else key.lower()
        shift = False

    text.insert("insert", key)


def create_keyboard():
    for widget in keyboard_frame.winfo_children():
        widget.destroy()

    for r, row in enumerate(layout):
        row_frame = tk.Frame(keyboard_frame)
        row_frame.pack(pady=6)  # spacing between rows (IMPORTANT)

        for key in row:
            width = 5

            if key == "Space":
                width = 40
            elif key in ["Shift","Backspace","Enter","Tab","Caps"]:
                width = 10

            btn = tk.Button(
                row_frame,
                text=key,
                font=("Arial", 14),
                width=width,
                height=2,
                command=lambda k=key: press(k)
            )
            btn.pack(side=tk.LEFT, padx=4, pady=2)


def explode_keyboard(count=5):
    for widget in keyboard_frame.winfo_children():
        widget.destroy()

    status.config(text=f"💥 Keyboard exploded! Returning in {count}...")

    if count > 0:
        root.after(1000, lambda: explode_keyboard(count - 1))
    else:
        status.config(text="")
        create_keyboard()


explode_btn = tk.Button(
    root,
    text="💥 EXPLODE KEYBOARD 💥",
    bg="red",
    fg="white",
    font=("Arial", 18, "bold"),
    command=lambda: explode_keyboard(5)
)
explode_btn.pack(pady=10)

create_keyboard()

root.mainloop()
