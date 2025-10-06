
import pygame
import random
import sys

# 🎮 Initialize the game
pygame.init()

# Screen settings
WIDTH, HEIGHT = 600, 400
screen = pygame.display.set_mode((WIDTH, HEIGHT))
pygame.display.set_caption("🚀 Dodge the Blocks")

# Colors
WHITE = (255, 255, 255)
RED   = (255, 50, 50)
BLUE  = (50, 120, 255)
BLACK = (0, 0, 0)

# Player settings
player_size = 50
player = pygame.Rect(WIDTH//2, HEIGHT - 70, player_size, player_size)
player_speed = 8

# Enemy settings
enemy_size = 50
enemy_list = []
enemy_speed = 6

# Score
score = 0
font = pygame.font.SysFont("Arial", 24)

# Clock
clock = pygame.time.Clock()

def drop_enemies(enemy_list):
    delay = random.random()
    if len(enemy_list) < 7 and delay < 0.1:
        x_pos = random.randint(0, WIDTH - enemy_size)
        enemy_list.append(pygame.Rect(x_pos, 0, enemy_size, enemy_size))

def draw_enemies(enemy_list):
    for enemy in enemy_list:
        pygame.draw.rect(screen, RED, enemy)

def update_enemy_positions(enemy_list, score):
    for idx, enemy in enumerate(enemy_list):
        if enemy.y >= 0 and enemy.y < HEIGHT:
            enemy.y += enemy_speed
        else:
            enemy_list.pop(idx)
            score += 1
    return score

def collision_check(enemy_list, player):
    for enemy in enemy_list:
        if player.colliderect(enemy):
            return True
    return False

# 🎮 Game Loop
while True:
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            pygame.quit()
            sys.exit()

    keys = pygame.key.get_pressed()
    if keys[pygame.K_LEFT] and player.left > 0:
        player.move_ip(-player_speed, 0)
    if keys[pygame.K_RIGHT] and player.right < WIDTH:
        player.move_ip(player_speed, 0)

    screen.fill(WHITE)

    drop_enemies(enemy_list)
    score = update_enemy_positions(enemy_list, score)

    if collision_check(enemy_list, player):
        print(f"💀 Game Over! Your score: {score}")
        pygame.quit()
        sys.exit()

    draw_enemies(enemy_list)
    pygame.draw.rect(screen, BLUE, player)

    score_text = font.render(f"Score: {score}", True, BLACK)
    screen.blit(score_text, (10, 10))

    pygame.display.flip()
    clock.tick(30)
