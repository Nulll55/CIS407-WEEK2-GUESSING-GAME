# CIS407-WEEK2-GUESSING-GAME
Java guessing game 1-100



package guessingGame;

import java.util.Random;

public class Game {
    private int number;
    private int guessNumber;
    private int counter;

    public void generateNumberToBeGuessed() {
        Random rand = new Random();
        number = rand.nextInt(100) + 1;
        counter = 0;
    }

    public void makeGuess(int guess) {
        guessNumber = guess;
        counter++;
    }

    public boolean isCorrectGuess() {
        return guessNumber == number;
    }

    public void displayWelcomeMessage() {
        System.out.println("Welcome to the Guessing Game!");
        System.out.println("Try to guess the number between 1 and 100.\n");
    }

    public void displayPleaseGuessMessage() {
        System.out.println("Please enter your guess (1–100):");
    }

    public void displayCorrectGuessMessage() {
        System.out.println("\nCongratulations! You guessed the number " + number + " correctly!");
        if (counter <= 3) {
            System.out.println("Great work! You are a mathematical wizard.");
        } else if (counter <= 7) {
            System.out.println("Not too bad! You've got some potential.");
        } else {
            System.out.println("What took you so long?");
        }
    }

    public void displayGuessAgainMessage() {
        int difference = guessNumber - number;
        if (difference > 10) {
            System.out.println("Way too high! Guess again.");
        } else if (difference > 0) {
            System.out.println("Too high! Guess again.");
        } else if (difference < -10) {
            System.out.println("Way too low! Guess again.");
        } else {
            System.out.println("Too low! Guess again.");
        }
    }
}

------------------------
package guessingGame;

import java.util.Scanner;

public class GuessNumberApp {
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        Game game = new Game();
        String playAgain = "y";

        game.displayWelcomeMessage();

        while (playAgain.equalsIgnoreCase("y")) {
            game.generateNumberToBeGuessed();
            game.displayPleaseGuessMessage();

            boolean correct = false;
            while (!correct) {
                System.out.print("Enter your guess: ");
                int guess = sc.nextInt();
                game.makeGuess(guess);

                if (game.isCorrectGuess()) {
                    game.displayCorrectGuessMessage();
                    correct = true;
                } else {
                    game.displayGuessAgainMessage();
                }
            }

            System.out.print("\nWould you like to play again? (y/n): ");
            playAgain = sc.next();
            System.out.println();
        }

        System.out.println("Thanks for playing the Guessing Game!");
        sc.close();
    }
}

---------------------------------------- week3
import java.util.Scanner;

public class TicTacToe {
    private char[][] board;
    private int rowNumber;
    private int columnNumber;
    private char markSelected;
    private boolean gameOver;

    // Constructor
    public TicTacToe() {
        board = new char[3][3];
        initializeBoard();
        markSelected = 'X';
        gameOver = false;
    }

    // Initialize the board with empty spaces
    private void initializeBoard() {
        for (int i = 0; i < 3; i++) {
            for (int j = 0; j < 3; j++) {
                board[i][j] = ' ';
            }
        }
    }

    // Display welcome message
    public void displayWelcomeMessage() {
        System.out.println("Welcome to Tic Tac Toe!");
    }

    // Display the current game grid
    public void displayGrid() {
        System.out.println("+---+---+---+");
        for (int i = 0; i < 3; i++) {
            System.out.print("| ");
            for (int j = 0; j < 3; j++) {
                System.out.print(board[i][j] + " | ");
            }
            System.out.println();
            System.out.println("+---+---+---+");
        }
    }

    // Start the game loop
    public void startGame() {
        Scanner input = new Scanner(System.in);
        displayGrid();

        while (!gameOver) {
            takeTurn(input);
            displayGrid();
            if (checkForWinner()) {
                System.out.println("Player " + markSelected + " wins!");
                gameOver = true;
            } else if (isBoardFull()) {
                System.out.println("It's a draw!");
                gameOver = true;
            } else {
                switchPlayer();
            }
        }
        input.close();
    }

    // Take a player's turn
    public void takeTurn(Scanner input) {
        boolean validMove = false;

        while (!validMove) {
            System.out.println("Player " + markSelected + "'s turn");
            System.out.print("Pick a row (1, 2, 3): ");
            rowNumber = input.nextInt() - 1;

            System.out.print("Pick a column (1, 2, 3): ");
            columnNumber = input.nextInt() - 1;

            if (rowNumber < 0 || rowNumber >= 3 || columnNumber < 0 || columnNumber >= 3) {
                System.out.println("Invalid position. Try again.");
            } else if (board[rowNumber][columnNumber] != ' ') {
                System.out.println("That spot is already taken. Try again.");
            } else {
                board[rowNumber][columnNumber] = markSelected;
                validMove = true;
            }
        }
    }

    // Switch between players
    private void switchPlayer() {
        markSelected = (markSelected == 'X') ? 'O' : 'X';
    }

    // Check if the board is full
    private boolean isBoardFull() {
        for (int i = 0; i < 3; i++) {
            for (int j = 0; j < 3; j++) {
                if (board[i][j] == ' ') {
                    return false;
                }
            }
        }
        return true;
    }

    // Check for a winner
    public boolean checkForWinner() {
        // Check rows and columns
        for (int i = 0; i < 3; i++) {
            if ((board[i][0] == markSelected && board[i][1] == markSelected && board[i][2] == markSelected) ||
                (board[0][i] == markSelected && board[1][i] == markSelected && board[2][i] == markSelected)) {
                return true;
            }
        }

        // Check diagonals
        if ((board[0][0] == markSelected && board[1][1] == markSelected && board[2][2] == markSelected) ||
            (board[0][2] == markSelected && board[1][1] == markSelected && board[2][0] == markSelected)) {
            return true;
        }

        return false;
    }
}


---- app
public class TicTacToeApp {
    public static void main(String[] args) {
        TicTacToe game = new TicTacToe();
        game.displayWelcomeMessage();
        game.startGame();
    }
}

------------ extra credit final
package guessingGame;

import java.util.Random;

public class Game {
    private int number;
    private int guessNumber;
    private int counter;
    private int previousGuess = -1; // to track the previous guess

    public void generateNumberToBeGuessed() {
        Random rand = new Random();
        number = rand.nextInt(100) + 1;
        counter = 0;
        previousGuess = -1; // reset each new game
    }

    public void makeGuess(int guess) {
        guessNumber = guess;
        counter++;
    }

    public boolean isCorrectGuess() {
        return guessNumber == number;
    }

    public void displayWelcomeMessage() {
        System.out.println("Welcome to the Guessing Game!");
        System.out.println("Try to guess the number between 1 and 100.\n");
    }

    public void displayPleaseGuessMessage() {
        System.out.println("Please enter your guess (1–100):");
    }

    public void displayCorrectGuessMessage() {
        System.out.println("\n🎉 Congratulations! You guessed the number " + number + " correctly!");
        System.out.println("Total guesses: " + counter);
        if (counter <= 3) {
            System.out.println("Great work! You are a mathematical wizard.");
        } else if (counter <= 7) {
            System.out.println("Not too bad! You've got some potential.");
        } else {
            System.out.println("What took you so long?");
        }
    }

    public void displayHintMessage() {
        // Compare to correct number
        if (guessNumber < number) {
            System.out.println("Too low!");
        } else if (guessNumber > number) {
            System.out.println("Too high!");
        }

        // Compare distance from previous guess to determine warmer/colder
        if (previousGuess != -1) {
            int previousDiff = Math.abs(previousGuess - number);
            int currentDiff = Math.abs(guessNumber - number);

            if (currentDiff < previousDiff) {
                System.out.println("You're getting warmer 🔥");
            } else if (currentDiff > previousDiff) {
                System.out.println("You're getting colder ❄️");
            } else {
                System.out.println("No change — you're just as close as before!");
            }
        }

        // Update previous guess
        previousGuess = guessNumber;
        System.out.println("Total guesses so far: " + counter + "\n");
    }
}

------------ extra credit app
package guessingGame;

import java.util.Scanner;

public class GuessNumberApp {
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        Game game = new Game();
        String playAgain = "y";

        game.displayWelcomeMessage();

        while (playAgain.equalsIgnoreCase("y")) {
            game.generateNumberToBeGuessed();
            game.displayPleaseGuessMessage();

            boolean correct = false;
            while (!correct) {
                System.out.print("Enter your guess: ");
                int guess = sc.nextInt();
                game.makeGuess(guess);

                if (game.isCorrectGuess()) {
                    game.displayCorrectGuessMessage();
                    correct = true;
                } else {
                    game.displayHintMessage();
                }
            }

            System.out.print("\nWould you like to play again? (y/n): ");
            playAgain = sc.next();
            System.out.println();
        }

        System.out.println("Thanks for playing the Guessing Game!");
        sc.close();
    }
}






