# Snake_game
import tkinter as tk
import random

class SnakeGame:
    def __init__(self, root, width=400, height=400):
        self.root = root
        self.root.title("Snake Game")

        self.canvas = tk.Canvas(root, width=width, height=height, bg="black")
        self.canvas.pack()

        self.snake = [(100, 100), (90, 100), (80, 100)]
        self.direction = "Right"

        self.food = self.spawn_food()

        self.canvas.focus_set()  # Set focus to the canvas to capture keyboard events
        self.canvas.bind("<Key>", self.change_direction)  # Change bind to canvas

        self.update_interval = 100  # in milliseconds
        self.root.after(self.update_interval, self.update)


    def spawn_food(self):
        x = random.randint(1, 19) * 20
        y = random.randint(1, 19) * 20
        food = self.canvas.create_rectangle(x, y, x + 20, y + 20, fill="red")
        return food

    def change_direction(self, event):
        if event.keysym in ["Up", "Down", "Left", "Right"]:
            self.direction = event.keysym

    def update(self):
        head = self.snake[0]
        if self.direction == "Up":
            new_head = (head[0], head[1] - 20)
        elif self.direction == "Down":
            new_head = (head[0], head[1] + 20)
        elif self.direction == "Left":
            new_head = (head[0] - 20, head[1])
        elif self.direction == "Right":
            new_head = (head[0] + 20, head[1])

        self.snake.insert(0, new_head)

        if self.check_collision():
            self.game_over()
            return

        self.canvas.delete("snake")
        for segment in self.snake:
            self.canvas.create_rectangle(segment[0], segment[1], segment[0] + 20, segment[1] + 20, fill="green", tags="snake")

        if self.check_food():
            self.food = self.spawn_food()
        else:
            self.snake.pop()

        self.root.after(self.update_interval, self.update)

    def check_collision(self):
        head = self.snake[0]
        return (
                head[0] < 0
                or head[0] >= 400
                or head[1] < 0
                or head[1] >= 400
                or head in self.snake[1:]
        )

    def check_food(self):
        head = self.snake[0]
        food_coords = self.canvas.coords(self.food)
        return head[0] == food_coords[0] and head[1] == food_coords[1]

    def game_over(self):
        self.canvas.create_text(
            200, 200, text="Game Over", font=("Helvetica", 16), fill="white"
        )

if __name__ == "__main__":
    root = tk.Tk()
    snake_game = SnakeGame(root)
    root.mainloop()
