# Pygame1
pip install pygame

import pygame
import sys

# Initialize Pygame
pygame.init()

# Constants
WIDTH, HEIGHT = 800, 600
PADDLE_WIDTH, PADDLE_HEIGHT = 120, 20
PADDLE_SPEED = 10
BALL_RADIUS = 15
BALL_SPEED_X, BALL_SPEED_Y = 7, 7
BRICK_WIDTH, BRICK_HEIGHT = 80, 30
BRICK_ROWS, BRICK_COLS = 4, 10

# Colors
WHITE = (255, 255, 255)
BLUE = (0, 0, 255)
RED = (255, 0, 0)

# Create the game window
screen = pygame.display.set_mode((WIDTH, HEIGHT))
pygame.display.set_caption("Brick Breaker")

# Initialize paddle and ball positions
paddle_x = (WIDTH - PADDLE_WIDTH) // 2
paddle_y = HEIGHT - PADDLE_HEIGHT - 10
ball_x = WIDTH // 2
ball_y = HEIGHT // 2
ball_dx, ball_dy = BALL_SPEED_X, BALL_SPEED_Y

# Create bricks
bricks = []
for row in range(BRICK_ROWS):
    for col in range(BRICK_COLS):
        brick = pygame.Rect(col * (BRICK_WIDTH + 5) + 30, row * (BRICK_HEIGHT + 5) + 50, BRICK_WIDTH, BRICK_HEIGHT)
        bricks.append(brick)

# Main game loop
while True:
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            pygame.quit()
            sys.exit()

    # Move the paddle
    keys = pygame.key.get_pressed()
    if keys[pygame.K_LEFT] and paddle_x > 0:
        paddle_x -= PADDLE_SPEED
    if keys[pygame.K_RIGHT] and paddle_x < WIDTH - PADDLE_WIDTH:
        paddle_x += PADDLE_SPEED

    # Move the ball
    ball_x += ball_dx
    ball_y += ball_dy

    # Ball collision with walls
    if ball_x <= 0 or ball_x >= WIDTH:
        ball_dx *= -1
    if ball_y <= 0:
        ball_dy *= -1

    # Ball collision with paddle
    if (
        ball_y + BALL_RADIUS >= paddle_y
        and paddle_x <= ball_x <= paddle_x + PADDLE_WIDTH
    ):
        ball_dy *= -1

    # Ball collision with bricks
    for brick in bricks:
        if brick.colliderect(pygame.Rect(ball_x - BALL_RADIUS, ball_y - BALL_RADIUS, 2 * BALL_RADIUS, 2 * BALL_RADIUS)):
            ball_dy *= -1
            bricks.remove(brick)

    # Clear the screen
    screen.fill(WHITE)

    # Draw bricks
    for brick in bricks:
        pygame.draw.rect(screen, BLUE, brick)

    # Draw paddle
    pygame.draw.rect(screen, RED, (paddle_x, paddle_y, PADDLE_WIDTH, PADDLE_HEIGHT))

    # Draw the ball
    pygame.draw.circle(screen, RED, (ball_x, ball_y), BALL_RADIUS)

    # Update the display
    pygame.display.flip()

    # Check for game over
    if ball_y >= HEIGHT:
        font = pygame.font.Font(None, 74)
        text = font.render("Game Over", True, BLUE)
        screen.blit(text, (WIDTH // 2 - 200, HEIGHT // 2 - 50))
        pygame.display.flip()
        pygame.time.wait(2000)
        pygame.quit()
        sys.exit()
