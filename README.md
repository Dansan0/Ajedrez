# Ajedrez
un juego sencillo de ajedrez , basico,bueno en programacion python
import sys
import pygame
from pygame.locals import *


FPS = 30

WINDOWWIDTH = 480
WINDOWHEIGHT = 480
BOARDWIDTH = 8
BOARDHEIGHT = 8

PIECESIZE = 60

BGIMG = pygame.image.load('pics/chessboard.png')
WELCOME = pygame.image.load('pics/inicio.png')
WHITEWINS = pygame.image.load('pics/whitewins.png')
BLACKWINS = pygame.image.load('pics/blackwins.png')

#                 R   G	  B
WHITE =         (216,216,216)
BLACK =         ( 39, 39, 39)
HLCOLOR =       (  0,  0,200)


EMPTY = 'empty'

WHITEPAWN = 'whitepawn'
WHITEPAWNIMG = pygame.image.load('pics/whitepawn.png')
WHITEROOK = 'whiterook'
WHITEROOKIMG = pygame.image.load('pics/whiterook.png')
WHITEKNIGHT = 'whiteknight'
WHITEKNIGHTIMG = pygame.image.load('pics/whiteknight.png')
WHITEBISHOP = 'whitebishop'
WHITEBISHOPIMG = pygame.image.load('pics/whitebishop.png')
WHITEQUEEN = 'whitequeen'
WHITEQUEENIMG = pygame.image.load('pics/whitequeen.png')
WHITEKING = 'whiteking'
WHITEKINGIMG = pygame.image.load('pics/whiteking.png')
WHITEPIECE = [WHITEPAWN, WHITEROOK, WHITEKNIGHT, WHITEBISHOP, WHITEQUEEN, \
              WHITEKING]

BLACKPAWN = 'blackpawn'
BLACKPAWNIMG = pygame.image.load('pics/blackpawn.png')
BLACKROOK = 'blackrook'
BLACKROOKIMG = pygame.image.load('pics/blackrook.png')
BLACKKNIGHT = 'blackknight'
BLACKKNIGHTIMG = pygame.image.load('pics/blackknight.png')
BLACKBISHOP = 'blackbishop'
BLACKBISHOPIMG = pygame.image.load('pics/blackbishop.png')
BLACKQUEEN = 'blackqueen'
BLACKQUEENIMG = pygame.image.load('pics/blackqueen.png')
BLACKKING = 'blackking'
BLACKKINGIMG = pygame.image.load('pics/blackking.png')
BLACKPIECE = [BLACKPAWN, BLACKROOK, BLACKKNIGHT, BLACKBISHOP, BLACKQUEEN, \
              BLACKKING]


def main():
    global FPSCLOCK, DISPLAYSURF

    pygame.init()


    pygame.display.set_caption('RookChess')

    FPSCLOCK = pygame.time.Clock()


    DISPLAYSURF = pygame.display.set_mode((WINDOWWIDTH, WINDOWHEIGHT))


    while True:
        welcome_screen()

        if run_game() == False:
            break


def run_game():
    board = get_starting_board()
    turn = 'white'
    
    mousex = 0
    mousey = 0

    boxx = 0
    boxy = 0


    firstclick = True
    secondclick = False


    while True:

        for event in pygame.event.get():


            ESCAPE = (event.type == KEYUP and event.key == K_ESCAPE)


            if event.type == QUIT or ESCAPE:
                quit_game()


            elif event.type == MOUSEBUTTONUP:


                if firstclick == True:

                    mousex, mousey = event.pos
                    boxx, boxy = pixel_to_box(mousex, mousey)
                    selectedpiece = board[boxx][boxy]


                    if selectedpiece != EMPTY \
                            and right_color(turn, selectedpiece):

                        firstclick = False
                        secondclick = True


                elif secondclick == True:

                    mousex2, mousey2 = event.pos
                    targetx, targety = pixel_to_box(mousex2, mousey2)
                    targetpiece = board[targetx][targety]


                    if right_color(turn, targetpiece):
                        firstclick = True
                        secondclick = False


                    elif (targetpiece == EMPTY \
                          or valid_target(turn, targetpiece)) \
                          and valid_move(boxx, boxy, targetx, \
                          targety, selectedpiece, board):


                        if board[targetx][targety] == WHITEKING:
                            black_wins()

                        elif board[targetx][targety] == BLACKKING:
                            white_wins()

                        board[boxx][boxy] = EMPTY
                        board[targetx][targety] = selectedpiece

                        firstclick = True
                        secondclick = False
                        

                        turn = change_turn(turn)


                        board = pawn_to_queen(board, selectedpiece, \
                                              targetx, targety)


                    elif valid_move(boxx, boxy, targetx, targety, \
                                    selectedpiece, board) == False:
                        firstclick = True
                        secondclick = False



        DISPLAYSURF.blit(BGIMG, (0, 0))


        if secondclick == True:
            highlight_box(boxx, boxy)


        draw_board(board)

        pygame.display.update()
        FPSCLOCK.tick(FPS)


def get_starting_board():

    board = []


    for i in range(BOARDWIDTH):
        board.append([EMPTY] * BOARDHEIGHT)





    board[0][0] = BLACKROOK
    board[1][0] = BLACKKNIGHT
    board[2][0] = BLACKBISHOP
    board[3][0] = BLACKQUEEN
    board[4][0] = BLACKKING
    board[5][0] = BLACKBISHOP
    board[6][0] = BLACKKNIGHT
    board[7][0] = BLACKROOK
    board[0][1] = BLACKPAWN
    board[1][1] = BLACKPAWN
    board[2][1] = BLACKPAWN
    board[3][1] = BLACKPAWN
    board[4][1] = BLACKPAWN
    board[5][1] = BLACKPAWN 
    board[6][1] = BLACKPAWN
    board[7][1] = BLACKPAWN


    board[0][7] = WHITEROOK
    board[1][7] = WHITEKNIGHT
    board[2][7] = WHITEBISHOP
    board[3][7] = WHITEQUEEN
    board[4][7] = WHITEKING
    board[5][7] = WHITEBISHOP
    board[6][7] = WHITEKNIGHT
    board[7][7] = WHITEROOK
    board[0][6] = WHITEPAWN
    board[1][6] = WHITEPAWN
    board[2][6] = WHITEPAWN
    board[3][6] = WHITEPAWN
    board[4][6] = WHITEPAWN
    board[5][6] = WHITEPAWN
    board[6][6] = WHITEPAWN
    board[7][6] = WHITEPAWN

    return board


def draw_board(board):
    for x in range(BOARDWIDTH):
        for y in range(BOARDHEIGHT):
            if BLACKPAWN in board[x][y]:
                DISPLAYSURF.blit(BLACKPAWNIMG, (x * PIECESIZE, 
                                                y * PIECESIZE))
            if BLACKQUEEN in board[x][y]:
                DISPLAYSURF.blit(BLACKQUEENIMG, (x * PIECESIZE, 
                                                 y * PIECESIZE))
            if BLACKKING in board[x][y]:
                DISPLAYSURF.blit(BLACKKINGIMG, (x * PIECESIZE, 
                                                y * PIECESIZE))
            if BLACKROOK in board[x][y]:
                DISPLAYSURF.blit(BLACKROOKIMG, (x * PIECESIZE, 
                                                y * PIECESIZE))
            if BLACKKNIGHT in board[x][y]:
                DISPLAYSURF.blit(BLACKKNIGHTIMG, (x * PIECESIZE, 
                                                  y * PIECESIZE))
            if BLACKBISHOP in board[x][y]:
                DISPLAYSURF.blit(BLACKBISHOPIMG, (x * PIECESIZE, 
                                                  y * PIECESIZE))
            if WHITEPAWN in board[x][y]:
                DISPLAYSURF.blit(WHITEPAWNIMG, (x * PIECESIZE, 
                                                y * PIECESIZE))
            if WHITEQUEEN in board[x][y]:
                DISPLAYSURF.blit(WHITEQUEENIMG, (x * PIECESIZE, 
                                                 y * PIECESIZE))
            if WHITEKING in board[x][y]:
                DISPLAYSURF.blit(WHITEKINGIMG, (x * PIECESIZE, 
                                                y * PIECESIZE))
            if WHITEROOK in board[x][y]:
                DISPLAYSURF.blit(WHITEROOKIMG, (x * PIECESIZE, 
                                                y * PIECESIZE))
            if WHITEKNIGHT in board[x][y]:
                DISPLAYSURF.blit(WHITEKNIGHTIMG, (x * PIECESIZE, 
                                                  y * PIECESIZE))
            if WHITEBISHOP in board[x][y]:
                DISPLAYSURF.blit(WHITEBISHOPIMG, (x * PIECESIZE, 
                                                  y * PIECESIZE))


def highlight_box(boxx, boxy):

    left, top = top_left_box_coords(boxx, boxy)


    return pygame.draw.rect(DISPLAYSURF, HLCOLOR, \
                     (left, top, PIECESIZE, PIECESIZE), 3)


def top_left_box_coords(boxx, boxy):

    left = boxx * PIECESIZE
    top = boxy * PIECESIZE
    return (left, top)


def pixel_to_box(x, y):

    for boxx in range(BOARDWIDTH):
        for boxy in range(BOARDHEIGHT):
            left, top = top_left_box_coords(boxx, boxy)
            boxRect = pygame.Rect(left, top, PIECESIZE, PIECESIZE)
            if boxRect.collidepoint(x, y):
                return (boxx, boxy)
    return (None, None)


def change_turn(turn):

    if turn == 'white':
        turn = 'black'
    elif turn == 'black':
        turn = 'white'
    return turn


def right_color(turn, selectedpiece):

    if turn == 'white' and selectedpiece in WHITEPIECE:
        return True
    if turn == 'black' and selectedpiece in BLACKPIECE:
        return True


def valid_target(turn, targetpiece):
    if turn == 'white' and targetpiece in BLACKPIECE:
        return True
    if turn == 'black' and targetpiece in WHITEPIECE:
        return True


def valid_move(boxx, boxy, targetx, targety, selectedpiece, board):

    if selectedpiece == WHITEPAWN:
        validmove = white_pawn_moves(boxx, boxy, targetx, targety, board)
        return validmove
    elif selectedpiece == BLACKPAWN:
        validmove = black_pawn_moves(boxx, boxy, targetx, targety, board)
        return validmove
    elif selectedpiece[5:] == 'rook':
        validmove = rook_moves(boxx, boxy, targetx, targety, board)
        return validmove
    elif selectedpiece[5:] == 'knight':
        validmove = knight_moves(boxx, boxy, targetx, targety, board)
        return validmove
    elif selectedpiece[5:] == 'bishop':
        validmove = bishop_moves(boxx, boxy, targetx, targety, board)
        return validmove
    elif selectedpiece[5:] == 'queen':
        validmove = queen_moves(boxx, boxy, targetx, targety, board)
        return validmove
    elif selectedpiece[5:] == 'king':
        validmove = king_moves(boxx, boxy, targetx, targety, board)
        return validmove




def white_pawn_moves(boxx, boxy, targetx, targety, board):



    if boxy - targety == 1 and boxx - targetx == 0 \
            and board[targetx][targety] == EMPTY:
        return True


    elif boxy == 6 and boxy - targety == 2 and boxx - targetx == 0 \
            and board[targetx][targety] == EMPTY \
            and board[boxx][boxy - 1] == EMPTY:
        return True


    elif boxy - targety == 1 and (boxx - targetx == 1 or targetx - boxx == 1) \
            and board[targetx][targety] in BLACKPIECE:
        return True


def black_pawn_moves(boxx, boxy, targetx, targety, board):



    if targety - boxy == 1 and targetx - boxx == 0 \
            and board[targetx][targety] == EMPTY:
        return True


    elif boxy == 1 and targety - boxy == 2 and targetx - boxx == 0 \
            and board[targetx][targety] == EMPTY \
            and board[boxx][boxy + 1] == EMPTY:
        return True


    elif targety - boxy == 1 and (boxx - targetx == 1 or targetx - boxx == 1) \
            and board[targetx][targety] in WHITEPIECE:
        return True


def knight_moves(boxx, boxy, targetx, targety, board):



    if abs(targety - boxy) == 2 and abs(targetx - boxx) == 1 \
            or abs(targety - boxy) == 1 and abs(targetx - boxx) == 2:
        return True


def rook_moves(boxx, boxy, targetx, targety, board):



    if 0 < targetx - boxx < 8 and targety - boxy == 0:
        blocking = []
        for i in range(1, abs(targetx - boxx)):
            if board[boxx + i][boxy] != EMPTY:
                blocking.append(False)
            else:
                blocking.append(True)

        if False not in blocking:

            return True


    elif 0 > targetx - boxx > -8 and targety - boxy == 0:
        blocking = []
        for i in range(1, abs(targetx - boxx)):
            if board[boxx - i][boxy] != EMPTY:
                blocking.append(False)
            else:
                blocking.append(True)

        if False not in blocking:
            return True


    elif 0 < targety - boxy < 8 and targetx - boxx == 0:
        blocking = []
        for i in range(1, abs(targety - boxy)):
            if board[boxx][boxy + i] != EMPTY:
                blocking.append(False)
            else:
                blocking.append(True)

        if False not in blocking:
            return True


    elif 0 > targety - boxy > -8 and targetx - boxx == 0:
        blocking = []
        for i in range(1, abs(targety - boxy)):
            if board[boxx][boxy - i] != EMPTY:
                blocking.append(False)
            else:
                blocking.append(True)

        if False not in blocking:
            return True


def bishop_moves(boxx, boxy, targetx, targety, board):



    if 0 < targetx - boxx < 8 and 0 < targety - boxy < 8 \
            and (abs(targetx - boxx)) / (float(abs(targety - boxy))) == 1.0:
        blocking = []
        for i in range(1, abs(targetx - boxx)):
            if board[boxx + i][boxy + i] != EMPTY:
                blocking.append(False)
            else:
                blocking.append(True)

        if False not in blocking:
            return True


    elif 0 > targetx - boxx > -8 and 0 < targety - boxy < 8 \
            and (abs(targetx - boxx)) / (float(abs(targety - boxy))) == 1.0:
        blocking = []
        for i in range(1, abs(targetx - boxx)):
            if board[boxx - i][boxy + i] != EMPTY:
                blocking.append(False)
            else:
                blocking.append(True)

        if False not in blocking:
            return True


    elif 0 < targetx - boxx < 8 and 0 > targety - boxy > -8 \
            and (abs(targetx - boxx)) / (float(abs(targety - boxy))) == 1.0:
        blocking = []
        for i in range(1, abs(targety - boxy)):
            if board[boxx + i][boxy - i] != EMPTY:
                blocking.append(False)
            else:
                blocking.append(True)

        if False not in blocking:
            return True


    elif 0 > targetx - boxx > -8 and 0 > targety - boxy > -8 \
            and (abs(targetx - boxx)) / (float(abs(targety - boxy))) == 1.0:
        blocking = []
        for i in range(1, abs(targety - boxy)):
            if board[boxx - i][boxy - i] != EMPTY:
                blocking.append(False)
            else:
                blocking.append(True)

        if False not in blocking:
            return True

def queen_moves(boxx, boxy, targetx, targety, board):

 

    if 0 < targetx - boxx < 8 and targety - boxy == 0:
        blocking = []
        for i in range(1, abs(targetx - boxx)):
            if board[boxx + i][boxy] != EMPTY:
                blocking.append(False)
            else:
                blocking.append(True)

        if False not in blocking:

            return True


    elif 0 > targetx - boxx > -8 and targety - boxy == 0:
        blocking = []
        for i in range(1, abs(targetx - boxx)):
            if board[boxx - i][boxy] != EMPTY:
                blocking.append(False)
            else:
                blocking.append(True)

        if False not in blocking:
            return True


    elif 0 < targety - boxy < 8 and targetx - boxx == 0:
        blocking = []
        for i in range(1, abs(targety - boxy)):
            if board[boxx][boxy + i] != EMPTY:
                blocking.append(False)
            else:
                blocking.append(True)

        if False not in blocking:
            return True


    elif 0 > targety - boxy > -8 and targetx - boxx == 0:
        blocking = []
        for i in range(1, abs(targety - boxy)):
            if board[boxx][boxy - i] != EMPTY:
                blocking.append(False)
            else:
                blocking.append(True)

        if False not in blocking:
            return True


    elif 0 < targetx - boxx < 8 and 0 < targety - boxy < 8 \
            and (abs(targetx - boxx)) / (abs(targety - boxy)) == 1:
        blocking = []
        for i in range(1, abs(targetx - boxx)):
            if board[boxx + i][boxy + i] != EMPTY:
                blocking.append(False)
            else:
                blocking.append(True)

        if False not in blocking:
            return True


    elif 0 > targetx - boxx > -8 and 0 < targety - boxy < 8 \
            and (abs(targetx - boxx)) / (abs(targety - boxy)) == 1:
        blocking = []
        for i in range(1, abs(targetx - boxx)):
            if board[boxx - i][boxy + i] != EMPTY:
                blocking.append(False)
            else:
                blocking.append(True)

        if False not in blocking:
            return True


    elif 0 < targetx - boxx < 8 and 0 > targety - boxy > -8 \
            and (abs(targetx - boxx)) / (abs(targety - boxy)) == 1:
        blocking = []
        for i in range(1, abs(targety - boxy)):
            if board[boxx + i][boxy - i] != EMPTY:
                blocking.append(False)
            else:
                blocking.append(True)

        if False not in blocking:
            return True


    elif 0 > targetx - boxx > -8 and 0 > targety - boxy > -8 \
            and (abs(targetx - boxx)) / (abs(targety - boxy)) == 1:
        blocking = []
        for i in range(1, abs(targety - boxy)):
            if board[boxx - i][boxy - i] != EMPTY:
                blocking.append(False)
            else:
                blocking.append(True)

        if False not in blocking:
            return True


def king_moves(boxx, boxy, targetx, targety, board):



    if abs(targety - boxy) == 1 and targetx - boxx == 0 \
            or abs(targetx - boxx) == 1 and targety - boxy == 0:
        return True


    if abs(targety - boxy) == 1 and abs(targetx - boxx) == 1  \
            or abs(targetx - boxx) == 1 and abs(targety - boxy) == 1:
        return True


def pawn_to_queen(board, selectedpiece, targetx, targety):

    if selectedpiece == WHITEPAWN and targety == 0:
        board[targetx][targety] = WHITEQUEEN


    elif selectedpiece == BLACKPAWN and targety == 7:
        board[targetx][targety] = BLACKQUEEN

    return board


def white_wins():

    while True:
        for event in pygame.event.get():


            ESCAPE = (event.type == KEYUP and event.key == K_ESCAPE)


            if event.type == QUIT or ESCAPE:
                quit_game()
 

            if event.type == KEYUP and event.key == K_RETURN:
                run_game()


        DISPLAYSURF.fill(WHITE)
        DISPLAYSURF.blit(WHITEWINS, (0, 0))
        pygame.display.update()

def black_wins():

    while True:
        for event in pygame.event.get():


            ESCAPE = (event.type == KEYUP and event.key == K_ESCAPE)


            if event.type == QUIT or ESCAPE:
                quit_game()


            if event.type == KEYUP:
                run_game()


        DISPLAYSURF.fill(BLACK)
        DISPLAYSURF.blit(BLACKWINS, (0, 0))
        pygame.display.update()


def welcome_screen():

    while True:
        for event in pygame.event.get():


            ESCAPE = (event.type == KEYUP and event.key == K_ESCAPE)


            if event.type == QUIT or ESCAPE:
                quit_game()

            if event.type == KEYUP:
                return run_game()

        DISPLAYSURF.fill(BLACK)
        DISPLAYSURF.blit(WELCOME, (0, 0))
        pygame.display.update()


def quit_game():

    pygame.quit()
    sys.exit()


if __name__ == '__main__':
    main()
