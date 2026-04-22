import pygame
import random

# Initialisierung
pygame.init()

# Farben
WHITE = (255, 255, 255)
GREEN = (0, 200, 0)
RED = (200, 0, 0)
BLACK = (0, 0, 0)

# Fenstergröße
WIDTH, HEIGHT = 600, 400
BLOCK_SIZE = 20

screen = pygame.display.set_mode((WIDTH, HEIGHT))
pygame.display.set_caption("Snake Game")

clock = pygame.time.Clock()
FONT = pygame.font.SysFont("Arial", 25)

# Snake
snake = [(100, 100), (80, 100), (60, 100)]
direction = "RIGHT"

# Apfel
apple = (random.randrange(0, WIDTH, BLOCK_SIZE), random.randrange(0, HEIGHT, BLOCK_SIZE))

score = 0


def draw_snake(snake):
    for block in snake:
        pygame.draw.rect(screen, GREEN, (*block, BLOCK_SIZE, BLOCK_SIZE))


def draw_apple(pos):
    pygame.draw.rect(screen, RED, (*pos, BLOCK_SIZE, BLOCK_SIZE))


def show_score():
    text = FONT.render(f"Score: {score}", True, WHITE)
    screen.blit(text, (10, 10))


def move_snake(snake, direction):
    head_x, head_y = snake[0]

    if direction == "UP":
        head_y -= BLOCK_SIZE
    elif direction == "DOWN":
        head_y += BLOCK_SIZE
    elif direction == "LEFT":
        head_x -= BLOCK_SIZE
    elif direction == "RIGHT":
        head_x += BLOCK_SIZE

    new_head = (head_x, head_y)
    snake.insert(0, new_head)
    return snake


running = True

while running:
    screen.fill(BLACK)

    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False
        elif event.type == pygame.KEYDOWN:
            if event.key == pygame.K_UP and direction != "DOWN":
                direction = "UP"
            elif event.key == pygame.K_DOWN and direction != "UP":
                direction = "DOWN"
            elif event.key == pygame.K_LEFT and direction != "RIGHT":
                direction = "LEFT"
            elif event.key == pygame.K_RIGHT and direction != "LEFT":
                direction = "RIGHT"

    snake = move_snake(snake, direction)

    # Kollision mit Apfel
    if snake[0] == apple:
        apple = (random.randrange(0, WIDTH, BLOCK_SIZE), random.randrange(0, HEIGHT, BLOCK_SIZE))
        score += 1
    else:
        snake.pop()

    # Kollision mit Wand
    head_x, head_y = snake[0]
    if head_x < 0 or head_x >= WIDTH or head_y < 0 or head_y >= HEIGHT:
        running = False

    # Kollision mit sich selbst
    if snake[0] in snake[1:]:
        running = False

    draw_snake(snake)
    draw_apple(apple)
    show_score()

    pygame.display.update()
    clock.tick(10)

pygame.quit()
