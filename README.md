# tkinter_calculator
This is my simple calculator source code 
import pygame
import random
import sys

pygame.init()

# Ukuran layar
WIDTH = 900
HEIGHT = 400

screen = pygame.display.set_mode((WIDTH, HEIGHT))
pygame.display.set_caption("Dino Game")

clock = pygame.time.Clock()

# Warna
WHITE = (255, 255, 255)
BLACK = (0, 0, 0)
GREEN = (0, 150, 0)
DINO_COLOR = (50, 50, 50)

# Font
font = pygame.font.Font(None, 36)
big_font = pygame.font.Font(None, 70)

# Tanah
GROUND_Y = 330

# Dino
dino = pygame.Rect(100, GROUND_Y - 50, 40, 50)
velocity_y = 0
gravity = 1
jump_power = -18

# Kaktus
cactus = pygame.Rect(900, GROUND_Y - 50, 30, 50)
cactus_speed = 7

# Skor
score = 0
game_over = False


def reset_game():
    global dino, velocity_y, cactus, score
    dino.x = 100
    dino.y = GROUND_Y - 50
    velocity_y = 0

    cactus.x = WIDTH + 100
    cactus.y = GROUND_Y - 50

    score = 0


while True:
    for event in pygame.event.get():

        if event.type == pygame.QUIT:
            pygame.quit()
            sys.exit()

        # Tombol lompat
        if event.type == pygame.KEYDOWN:
            if event.key == pygame.K_SPACE:

                if game_over:
                    reset_game()
                    game_over = False

                elif dino.bottom >= GROUND_Y:
                    velocity_y = jump_power

    if not game_over:

        # Gravitasi
        velocity_y += gravity
        dino.y += velocity_y

        # Jangan sampai menembus tanah
        if dino.bottom >= GROUND_Y:
            dino.bottom = GROUND_Y
            velocity_y = 0

        # Gerakkan kaktus
        cactus.x -= cactus_speed

        # Jika kaktus keluar layar
        if cactus.right < 0:
            cactus.x = WIDTH + random.randint(100, 400)
            score += 1

        # Periksa tabrakan
        if dino.colliderect(cactus):
            game_over = True

    # -------------------------
    # GAMBAR
    # -------------------------

    screen.fill(WHITE)

    # Tanah
    pygame.draw.line(
        screen,
        BLACK,
        (0, GROUND_Y),
        (WIDTH, GROUND_Y),
        3
    )

    # Dino
    pygame.draw.rect(screen, DINO_COLOR, dino)

    # Mata Dino
    pygame.draw.rect(
        screen,
        WHITE,
        (dino.x + 25, dino.y + 10, 6, 6)
    )

    # Kaktus
    pygame.draw.rect(screen, GREEN, cactus)

    # Cabang kaktus
    pygame.draw.rect(
        screen,
        GREEN,
        (cactus.x - 10, cactus.y + 15, 10, 20)
    )

    pygame.draw.rect(
        screen,
        GREEN,
        (cactus.x + 30, cactus.y + 25, 10, 20)
    )

    # Skor
    score_text = font.render(
        f"Score: {score}",
        True,
        BLACK
    )

    screen.blit(score_text, (20, 20))

    # Game Over
    if game_over:

        text = big_font.render(
            "GAME OVER",
            True,
            BLACK
        )

        restart = font.render(
            "Tekan SPACE untuk bermain lagi",
            True,
            BLACK
        )

        screen.blit(
            text,
            (WIDTH // 2 - text.get_width() // 2, 130)
        )

        screen.blit(
            restart,
            (WIDTH // 2 - restart.get_width() // 2, 210)
        )

    pygame.display.flip()

    clock.tick(60)
