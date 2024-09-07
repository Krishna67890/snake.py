# snake.py
this is my 1st code for snake game
<br>
Author - Krishna Patil Rajput

                                               //Snake game//
import tkinter
import random

ROWS = 25
COLS = 25
TILE_SIZE = 25

WINDOW_WIDTH = TILE_SIZE * ROWS
WINDOW_HEIGHT = TILE_SIZE * COLS

class Tile:
    def __init__(self, x, y):
        self.x = x
        self.y = y

# Game window
window = tkinter.Tk()
window.title("Snake")
window.resizable(False, False)

canvas = tkinter.Canvas(window, width=WINDOW_WIDTH, height=WINDOW_HEIGHT, bg="black", borderwidth=0, highlightthickness=0)
canvas.pack()
window.update()

# Center the window
window_width = window.winfo_width()
window_height = window.winfo_height()
screen_width = window.winfo_screenwidth()
screen_height = window.winfo_screenheight()

window_x = int((screen_width/2) - (window_width/2))
window_y = int((screen_height/2) - (window_height/2))
window.geometry(f"{window_width}x{window_height}+{window_x}+{window_y}")

# Initialize game
snake = Tile(5 * TILE_SIZE, 5 * TILE_SIZE)  # Single tile snake's head
food = Tile(10 * TILE_SIZE, 10 * TILE_SIZE)
snake_body = []  # Multiple snake tiles
velocityX = 0
velocityY = 0
game_over = False
score = 0

def change_direction(e):  # e = event
    global velocityX, velocityY, game_over
    if game_over:
        return

    if e.keysym == "Up" and velocityY != 1:
        velocityX = 0
        velocityY = -1
    elif e.keysym == "Down" and velocityY != -1:
        velocityX = 0
        velocityY = 1
    elif e.keysym == "Left" and velocityX != 1:
        velocityX = -1
        velocityY = 0
    elif e.keysym == "Right" and velocityX != -1:
        velocityX = 1
        velocityY = 0

def move():
    global snake, food, snake_body, game_over, score
    if game_over:
        return

    # Collision with walls
    if snake.x < 0 or snake.x >= WINDOW_WIDTH or snake.y < 0 or snake.y >= WINDOW_HEIGHT:
        game_over = True
        return

    # Collision with itself
    for tile in snake_body:
        if snake.x == tile.x and snake.y == tile.y:
            game_over = True
            return

    # Collision with food
    if snake.x == food.x and snake.y == food.y:
        snake_body.append(Tile(snake.x, snake.y))
        food.x = random.randint(0, ROWS - 1) * TILE_SIZE
        food.y = random.randint(0, COLS - 1) * TILE_SIZE
        score += 1

    # Update snake body
    if snake_body:
        for i in range(len(snake_body) - 1, 0, -1):
            snake_body[i].x = snake_body[i - 1].x
            snake_body[i].y = snake_body[i - 1].y

        snake_body[0].x = snake.x
        snake_body[0].y = snake.y

    snake.x += velocityX * TILE_SIZE
    snake.y += velocityY * TILE_SIZE

def draw():
    global snake, food, snake_body, game_over, score
    move()

    canvas.delete("all")

    # Draw food
    canvas.create_rectangle(food.x, food.y, food.x + TILE_SIZE, food.y + TILE_SIZE, fill="red")

    # Draw snake
    canvas.create_rectangle(snake.x, snake.y, snake.x + TILE_SIZE, snake.y + TILE_SIZE, fill="lime green")

    for tile in snake_body:
        canvas.create_rectangle(tile.x, tile.y, tile.x + TILE_SIZE, tile.y + TILE_SIZE, fill="lime green")

    # Display game over or score
    if game_over:
        canvas.create_text(WINDOW_WIDTH / 2, WINDOW_HEIGHT / 2, text=f"Game Over! Score: {score}", font=("Arial", 20), fill="white")
    else:
        canvas.create_text(WINDOW_WIDTH / 2, 20, text=f"Score: {score}", font=("Arial", 20), fill="white")

    window.after(100, draw)  # 100 ms = 1/10 second, 10 frames per second

draw()

window.bind("<KeyRelease>", change_direction)
window.mainloop()
