# tiktaktoe_codesoft_internship
import math
import random
import copy

def creation_board(board):
    print("| {} | {} | {} |".format(board[0][0], board[0][1], board[0][2]))
    print("------------")
    print("| {} | {} | {} |".format(board[1][0], board[1][1], board[1][2]))
    print("------------")
    print("| {} | {} | {} |".format(board[2][0], board[2][1], board[2][2]))

def evaluation(board):
    # Évaluation de la position actuelle du plateau
    # en fonction du nombre de jetons alignés et de leur potentiel pour former un alignement gagnant
    score = 0
    for i in range(3):
        if board[i][0] == board[i][1] == board[i][2] != " ":
            if board[i][0] == "X":
                score += 1
            else:
                score -= 1
        if board[0][i] == board[1][i] == board[2][i] != " ":
            if board[0][i] == "X":
                score += 1
            else:
                score -= 1
    if board[0][0] == board[1][1] == board[2][2] != " ":
        if board[0][0] == "X":
            score += 1
        else:
            score -= 1
    if board[0][2] == board[1][1] == board[2][0] != " ":
        if board[0][2] == "X":
            score += 1
        else:
            score -= 1
    return score

def check_the_winner(board):
    for i in range(3):
        if board[i][0] == board[i][1] == board[i][2] != " ":
            return board[i][0], "Win !"
        if board[0][i] == board[1][i] == board[2][i] != " ":
            return board[0][i], "Win !"
    if board[0][0] == board[1][1] == board[2][2] != " ":
        return board[0][0], "Win !"
    if board[0][2] == board[1][1] == board[2][0] != " ":
        return board[0][2], "Win !"
    if all(board[i][j] != " " for i in range(3) for j in range(3)):
        return "Match nul"
    return None

def get_player_move(player,board):
    while True:
        try:
            row = int(input(f"Player {player}, select the row (0-2): "))
            col = int(input(f"Player {player}, select the column (0-2): "))
            if not (0 <= row < 3 and 0 <= col < 3):
                raise ValueError
            if board[row][col] != " ":
                raise ValueError
            return row, col
        except ValueError:
            print("Invalid input. Please try again.")

def minimax(board, depth, is_maximizing):
    winner = check_the_winner(board)
    if winner is not None:
        if winner[1] == "Win !":
            if winner[0] == "X":
                return 1 * depth
            else:
                return -1 * depth
        else:  # Match nul
            return 0

    if depth == 0:
        return evaluation(board)

    if is_maximizing:
        best_score = -math.inf
        for i in range(3):
            for j in range(3):
                if board[i][j] == " ":
                    board[i][j] = "X"
                    score = minimax(board, depth - 1, False)
                    board[i][j] = " "
                    best_score = max(score, best_score)
        return best_score
    else:
        best_score = math.inf
        for i in range(3):
            for j in range(3):
                if board[i][j] == " ":
                    board[i][j] = "O"
                    score = minimax(board, depth - 1, True)
                    board[i][j] = " "
                    best_score = min(score, best_score)
        return best_score

def find_best_move(board):
    empty_cells = [(i, j) for i in range(3) for j in range(3) if board[i][j] == " "]
    random.shuffle(empty_cells)  # Mélanger l'ordre des cases vides
    best_score = -math.inf
    best_move = None
    for row, col in empty_cells:
        board_copy = copy.deepcopy(board)  # Créer une copie profonde du plateau
        board_copy[row][col] = "X"  # Modifier la copie du plateau
        score = minimax(board_copy, 9, False)  # Utiliser la copie pour minimax
        if score > best_score:
            best_score = score
            best_move = (row, col)
    return best_move

def play_with_ai(board):
    current_player = "O"  # L'IA commence
    while check_the_winner(board) is None:
        if current_player == "X":  # Tour du joueur humain
            creation_board(board)
            row, col = get_player_move(current_player,board)
        else:  # Tour de l'IA
            row, col = find_best_move(board)
        board[row][col] = current_player
        current_player = "O" if current_player == "X" else "X"
    creation_board(board)
    print(check_the_winner(board))

def main_with_ai():
    board = [[" ", " ", " "], [" ", " ", " "], [" ", " ", " "]]
    play_with_ai(board)

if __name__ == "__main__":
    main_with_ai()
