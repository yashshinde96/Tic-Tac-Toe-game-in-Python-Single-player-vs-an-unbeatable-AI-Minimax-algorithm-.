import math

def print_board(b):
    print()
    for i in range(3):
        print(' ' + ' | '.join(b[3*i:3*i+3]))
        if i < 2:
            print("---+---+---")
    print()

def winner(b):
    lines = [(0,1,2),(3,4,5),(6,7,8),(0,3,6),(1,4,7),(2,5,8),(0,4,8),(2,4,6)]
    for a,b,c in lines:
        if board[a] == board[b] == board[c] and board[a] in ('X','O'):
            return board[a]
    return None

def available_moves(b):
    return [i for i,cell in enumerate(b) if cell not in ('X','O')]

def minimax(b, is_maximizing, ai, human):
    win = winner(b)
    if win == ai:
        return 1
    elif win == human:
        return -1
    elif not available_moves(b):
        return 0

    if is_maximizing:
        best = -math.inf
        for m in available_moves(b):
            b[m] = ai
            val = minimax(b, False, ai, human)
            b[m] = str(m+1)
            best = max(best, val)
        return best
    else:
        best = math.inf
        for m in available_moves(b):
            b[m] = human
            val = minimax(b, True, ai, human)
            b[m] = str(m+1)
            best = min(best, val)
        return best

def ai_move(b, ai, human):
    best_score = -math.inf
    best_move = None
    for m in available_moves(b):
        b[m] = ai
        score = minimax(b, False, ai, human)
        b[m] = str(m+1)
        if score > best_score:
            best_score = score
            best_move = m
    return best_move

board = [str(i+1) for i in range(9)]
human = ''
ai = ''

while human not in ('X','O'):
    human = input("Choose X or O (X goes first): ").upper()
ai = 'O' if human == 'X' else 'X'

current = 'X'
print_board(board)

while True:
    if current == human:
        try:
            move = int(input(f"Your turn ({human}). Choose 1-9: ")) - 1
            if move < 0 or move > 8 or board[move] in ('X','O'):
                print("Invalid move. Try again.")
                continue
            board[move] = human
        except ValueError:
            print("Please enter a number 1-9.")
            continue
    else:
        print(f"AI ({ai}) is thinking...")
        move = ai_move(board, ai, human)
        board[move] = ai

    print_board(board)
    w = winner(board)
    if w:
        if w == human:
            print("You win! 🎉")
        else:
            print("AI wins. Better luck next time!")
        break
    if not available_moves(board):
        print("It's a draw!")
        break
    current = ai if current == human else human
