import pygame
import random

# Inicializar Pygame
pygame.init()

# Definir colores
NEGRO = (0, 0, 0)
BLANCO = (255, 255, 255)

# Definir dimensiones de la pantalla
ANCHO_PANTALLA = 800
ALTO_PANTALLA = 600

# Crear la pantalla
pantalla = pygame.display.set_mode((ANCHO_PANTALLA, ALTO_PANTALLA))
pygame.display.set_caption("Bob Esponja esquivando medusas")

# Cargar imágenes
bob_img = pygame.image.load("bob_esponja.png")
medusa_img = pygame.image.load("medusa.png")

# Escalar imágenes
bob_img = pygame.transform.scale(bob_img, (50, 50))
medusa_img = pygame.transform.scale(medusa_img, (50, 50))

# Clase para Bob Esponja
class Bob(pygame.sprite.Sprite):
    def __init__(self):
        super().__init__()
        self.image = bob_img
        self.rect = self.image.get_rect()
        self.rect.x = ANCHO_PANTALLA // 2
        self.rect.y = ALTO_PANTALLA - 60

    def update(self):
        keys = pygame.key.get_pressed()
        if keys[pygame.K_LEFT] and self.rect.x > 0:
            self.rect.x -= 5
        if keys[pygame.K_RIGHT] and self.rect.x < ANCHO_PANTALLA - 50:
            self.rect.x += 5

# Clase para las medusas
class Medusa(pygame.sprite.Sprite):
    def __init__(self):
        super().__init__()
        self.image = medusa_img
        self.rect = self.image.get_rect()
        self.rect.x = random.randint(0, ANCHO_PANTALLA - 50)
        self.rect.y = random.randint(-100, -40)
        self.speed_y = random.randint(3, 8)

    def update(self):
        self.rect.y += self.speed_y
        if self.rect.y > ALTO_PANTALLA:
            self.rect.y = random.randint(-100, -40)
            self.rect.x = random.randint(0, ANCHO_PANTALLA - 50)
            self.speed_y = random.randint(3, 8)

# Crear grupos de sprites
todos_sprites = pygame.sprite.Group()
medusas = pygame.sprite.Group()

# Crear instancia de Bob Esponja
bob = Bob()
todos_sprites.add(bob)

# Crear instancias de medusas
for i in range(10):
    medusa = Medusa()
    todos_sprites.add(medusa)
    medusas.add(medusa)

# Bucle principal del juego
corriendo = True
reloj = pygame.time.Clock()

while corriendo:
    # Manejo de eventos
    for evento in pygame.event.get():
        if evento.type == pygame.QUIT:
            corriendo = False

    # Actualizar sprites
    todos_sprites.update()

    # Verificar colisiones
    if pygame.sprite.spritecollideany(bob, medusas):
        bob.rect.x = ANCHO_PANTALLA // 2
        bob.rect.y = ALTO_PANTALLA - 60
        for medusa in medusas:
            medusa.rect.y = random.randint(-100, -40)
            medusa.rect.x = random.randint(0, ANCHO_PANTALLA - 50)

    # Dibujar todo
    pantalla.fill(BLANCO)
    todos_sprites.draw(pantalla)

    # Actualizar la pantalla
    pygame.display.flip()

    # Controlar la velocidad del juego
    reloj.tick(60)

# Salir de Pygame
pygame.quit()

