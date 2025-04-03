import pygame
import heapq

# Definimos el tamaño de la cuadrícula
ROWS, COLS = 20, 20  # Número de filas y columnas
TILE_SIZE = 30       # Tamaño de cada celda

# Colores
WHITE = (255, 255, 255)
BLACK = (0, 0, 0)
GREEN = (0, 255, 0)
RED = (255, 0, 0)
BLUE = (0, 0, 255)
GRAY = (200, 200, 200)

# Inicializa Pygame
pygame.init()
WIN = pygame.display.set_mode((COLS * TILE_SIZE, ROWS * TILE_SIZE))
pygame.display.set_caption("Algoritmo A*")

class Nodo:
    def __init__(self, fila, col):
        self.fila = fila
        self.col = col
        self.g = float('inf')  # Costo desde el inicio
        self.h = 0  # Heurística
        self.f = float('inf')  # Costo total
        self.padre = None
        self.es_obstaculo = False

    def __lt__(self, otro):
        return self.f < otro.f

    def dibujar(self, color):
        pygame.draw.rect(WIN, color, (self.col * TILE_SIZE, self.fila * TILE_SIZE, TILE_SIZE, TILE_SIZE))
        pygame.draw.rect(WIN, BLACK, (self.col * TILE_SIZE, self.fila * TILE_SIZE, TILE_SIZE, TILE_SIZE), 1)

def heuristica(nodo, objetivo):
    return 10 * (abs(nodo.fila - objetivo.fila) + abs(nodo.col - objetivo.col))

def reconstruir_camino(nodo):
    camino = []
    while nodo:
        camino.append(nodo)
        nodo = nodo.padre
    return camino[::-1]

def a_estrella(grid, inicio, objetivo):
    lista_abierta = []
    lista_cerrada = set()
    
    inicio.g = 0
    inicio.h = heuristica(inicio, objetivo)
    inicio.f = inicio.g + inicio.h
    heapq.heappush(lista_abierta, inicio)

    movimientos = [
        (-1, 0, 10), (1, 0, 10),  # Arriba, Abajo
        (0, -1, 10), (0, 1, 10),  # Izquierda, Derecha
        (-1, -1, 14), (-1, 1, 14),  # Diagonales
        (1, -1, 14), (1, 1, 14)
    ]

    while lista_abierta:
        actual = heapq.heappop(lista_abierta)
        lista_cerrada.add((actual.fila, actual.col))

        if actual == objetivo:
            return reconstruir_camino(actual), lista_cerrada

        for dx, dy, costo_mov in movimientos:
            fila_nueva, col_nueva = actual.fila + dx, actual.col + dy

            if 0 <= fila_nueva < ROWS and 0 <= col_nueva < COLS:
                vecino = grid[fila_nueva][col_nueva]

                if vecino.es_obstaculo or (vecino.fila, vecino.col) in lista_cerrada:
                    continue

                nuevo_g = actual.g + costo_mov

                if nuevo_g < vecino.g:
                    vecino.g = nuevo_g
                    vecino.h = heuristica(vecino, objetivo)
                    vecino.f = vecino.g + vecino.h
                    vecino.padre = actual
                    heapq.heappush(lista_abierta, vecino)

    return [], lista_cerrada  # No hay camino

# Generar la cuadrícula
grid = [[Nodo(fila, col) for col in range(COLS)] for fila in range(ROWS)]

# Variables de control
inicio = None
objetivo = None
fase = 0  # 0: Seleccionar inicio, 1: Seleccionar fin, 2: Seleccionar obstáculos, 3: Ejecutar A*
camino = []
lista_cerrada = set()

# Bucle principal
corriendo = True
while corriendo:
    WIN.fill(WHITE)

    for fila in grid:
        for nodo in fila:
            if nodo.es_obstaculo:
                nodo.dibujar(BLACK)
            elif nodo in camino:
                nodo.dibujar(GREEN)
            elif (nodo.fila, nodo.col) in lista_cerrada:
                nodo.dibujar(GRAY)
    
    if inicio:
        inicio.dibujar(BLUE)
    if objetivo:
        objetivo.dibujar(RED)

    for evento in pygame.event.get():
        if evento.type == pygame.QUIT:
            corriendo = False

        elif evento.type == pygame.MOUSEBUTTONDOWN:
            x, y = pygame.mouse.get_pos()
            fila, col = y // TILE_SIZE, x // TILE_SIZE
            
            if fase == 0:  # Seleccionar inicio
                inicio = grid[fila][col]
                fase = 1
            elif fase == 1:  # Seleccionar fin
                if (fila, col) != (inicio.fila, inicio.col):
                    objetivo = grid[fila][col]
                    fase = 2
            elif fase == 2:  # Colocar obstáculos
                if (fila, col) != (inicio.fila, inicio.col) and (fila, col) != (objetivo.fila, objetivo.col):
                    grid[fila][col].es_obstaculo = not grid[fila][col].es_obstaculo

        elif evento.type == pygame.KEYDOWN:
            if evento.key == pygame.K_SPACE and fase == 2:  # Ejecutar A*
                fase = 3
                for fila in grid:
                    for nodo in fila:
                        nodo.g = float('inf')
                        nodo.f = float('inf')
                        nodo.padre = None
                camino, lista_cerrada = a_estrella(grid, inicio, objetivo)
    
    pygame.display.update()

pygame.quit()
