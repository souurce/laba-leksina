[10 lab.py](https://github.com/user-attachments/files/25739743/10.lab.py)import tkinter as tk
from tkinter import messagebox

class TicTacToe:
    def __init__(self):
        self.window = tk.Tk()
        self.window.title("Крестики-нолики")
        self.window.resizable(False, False)
        self.current_player = "X"
        self.board = [""] * 9
        self.buttons = []
        self.create_board()
        self.update_status()
        
    def create_board(self):
        self.status_label = tk.Label(self.window, text="", font=('Arial', 14))
        self.status_label.grid(row=0, column=0, columnspan=3, pady=10)
        
        for i in range(9):
            row, col = divmod(i, 3)
            button = tk.Button(self.window, text="", font=('Arial', 20), width=5, height=2,
                             command=lambda idx=i: self.human_move(idx))
            button.grid(row=row+1, column=col, padx=2, pady=2)
            self.buttons.append(button)
        restart_btn = tk.Button(self.window, text="Новая игра", font=('Arial', 12),
                               command=self.restart_game)
        restart_btn.grid(row=4, column=0, columnspan=3, pady=10)
    def update_status(self):
        status = f"Ход: {'Игрок (X)' if self.current_player == 'X' else 'Бот (O)'}"
        self.status_label.config(text=status)

    def human_move(self, position):
        if self.board[position] == "" and self.current_player == "X":
            self.make_move(position, "X")
            if not self.check_game_over():
                self.current_player = "O"
                self.update_status()
                self.window.after(500, self.bot_move)
    
    def bot_move(self):
        if self.current_player == "O":
            best_score = float('-inf')
            best_move = None
            
            for i in range(9):
                if self.board[i] == "":
                    self.board[i] = "O"
                    score = self.minimax(self.board, 0, False, float('-inf'), float('inf'))
                    self.board[i] = ""
                    
                    if score > best_score:
                        best_score = score
                        best_move = i
            
            if best_move is not None:
                self.make_move(best_move, "O")
                if not self.check_game_over():
                    self.current_player = "X"
                    self.update_status()
    
    def minimax(self, board, depth, is_maximizing, alpha, beta):
        scores = {"X": -10 + depth, "O": 10 - depth, "tie": 0}
        winner = self.check_winner(board)
        if winner:
            return scores[winner]
        
        if self.is_board_full(board):
            return scores["tie"]
        
        if is_maximizing:
            best_score = float('-inf')
            for i in range(9):
                if board[i] == "":
                    board[i] = "O"
                    score = self.minimax(board, depth + 1, False, alpha, beta)
                    board[i] = ""
                    best_score = max(score, best_score)
                    alpha = max(alpha, best_score)
                    if beta <= alpha:
                        break
            return best_score
        else:
            best_score = float('inf')
            for i in range(9):
                if board[i] == "":
                    board[i] = "X"
                    score = self.minimax(board, depth + 1, True, alpha, beta)
                    board[i] = ""
                    best_score = min(score, best_score)
                    beta = min(beta, best_score)
                    if beta <= alpha:
                        break
            return best_score
    
    def check_winner(self, board=None):
        if board is None:
            board = self.board
            
        win_combinations = [
            [0, 1, 2], [3, 4, 5], [6, 7, 8],  
            [0, 3, 6], [1, 4, 7], [2, 5, 8],  
            [0, 4, 8], [2, 4, 6]             
        ]
        
        for combo in win_combinations:
            if board[combo[0]] == board[combo[1]] == board[combo[2]] != "":
                return board[combo[0]]
        return None
    
    def is_board_full(self, board=None):
        if board is None:
            board = self.board
        return "" not in board
    
    def make_move(self, position, player):
        self.board[position] = player
        self.buttons[position].config(text=player, 
                                    state="disabled",
                                    bg="light blue" if player == "X" else "light coral")
    
    def check_game_over(self):
        winner = self.check_winner()
        if winner:
            winner_text = "Игрок (X) победил!" if winner == "X" else "Бот (O) победил!"
            messagebox.showinfo("Игра окончена", winner_text)
            self.disable_all_buttons()
            return True
        elif self.is_board_full():
            messagebox.showinfo("Игра окончена", "Ничья!")
            self.disable_all_buttons()
            return True
        return False
    
    def disable_all_buttons(self):
        for button in self.buttons:
            button.config(state="disabled")
    def restart_game(self):
        self.current_player = "X"
        self.board = [""] * 9
        for button in self.buttons:
            button.config(text="", state="normal", bg="SystemButtonFace")
        self.update_status()
    def run(self):
        self.window.mainloop()
if __name__ == "__main__":
    game = TicTacToe()
    game.run()
