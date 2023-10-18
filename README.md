# python-snake-game
python code for snake game


pip install pygame
import pygame
import sys
import random

# Initialize Pygame
pygame.init()

# Constants
WIDTH, HEIGHT = 640, 480
GRID_SIZE = 20
GRID_WIDTH = WIDTH // GRID_SIZE
GRID_HEIGHT = HEIGHT // GRID_SIZE
BLACK = (0, 0, 0)
GREEN = (0, 255, 0)
RED = (255, 0, 0)

# Snake class
class Snake:
    def __init__(self):
        self.body = [(GRID_WIDTH // 2, GRID_HEIGHT // 2)]
        self.direction = (0, -1)

    def move(self, food):
        new_head = (self.body[0][0] + self.direction[0], self.body[0][1] + self.direction[1])
        if new_head == food:
            self.body.insert(0, food)
            return True
        else:
            self.body.insert(0, new_head)
            self.body.pop()
            return False

    def change_direction(self, new_direction):
        if (new_direction[0], new_direction[1]) != (-self.direction[0], -self.direction[1]):
            self.direction = new_direction

# Food class
class Food:
    def __init__(self):
        self.position = (random.randint(0, GRID_WIDTH - 1), random.randint(0, GRID_HEIGHT - 1))

    def randomize_position(self):
        self.position = (random.randint(0, GRID_WIDTH - 1), random.randint(0, GRID_HEIGHT - 1))

# Initialize the screen
screen = pygame.display.set_mode((WIDTH, HEIGHT))
pygame.display.set_caption('Snake Game')

snake = Snake()
food = Food()
clock = pygame.time.Clock()

game_over = False

while not game_over:
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            game_over = True
        elif event.type == pygame.KEYDOWN:
            if event.key == pygame.K_UP:
                snake.change_direction((0, -1))
            elif event.key == pygame.K_DOWN:
                snake.change_direction((0, 1))
            elif event.key == pygame.K_LEFT:
                snake.change_direction((-1, 0))
            elif event.key == pygame.K_RIGHT:
                snake.change_direction((1, 0))

    food_eaten = snake.move(food.position)
    if food_eaten:
        food.randomize_position()

    # Check for collisions with walls
    if (
        snake.body[0][0] < 0
        or snake.body[0][0] >= GRID_WIDTH
        or snake.body[0][1] < 0
        or snake.body[0][1] >= GRID_HEIGHT
    ):
        game_over = True

    # Check for collisions with itself
    for segment in snake.body[1:]:
        if segment == snake.body[0]:
            game_over = True

    # Clear the screen
    screen.fill(BLACK)

    # Draw the snake
    for segment in snake.body:
        pygame.draw.rect(
            screen, GREEN, (segment[0] * GRID_SIZE, segment[1] * GRID_SIZE, GRID_SIZE, GRID_SIZE)
        )

    # Draw the food
    pygame.draw.rect(
        screen, RED, (food.position[0] * GRID_SIZE, food.position[1] * GRID_SIZE, GRID_SIZE, GRID_SIZE)
    )

    # Update the display
    pygame.display.flip()

    # Cap the frame rate
    clock.tick(10)  # Adjust this value to control the game speed

pygame.quit()
sys.exit()
