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
    private char[][] board = new char[3][3];
    private char currentMark = 'X';

    public TicTacToe() {
        resetBoard();
    }

    public void displayWelcomeMessage() {
        System.out.println("Welcome to Tic Tac Toe!");
        System.out.println("Player X goes first.");
    }

    public void displayGrid() {
        System.out.println("-------------");
        for (int i = 0; i < 3; i++) {
            System.out.print("| ");
            for (int j = 0; j < 3; j++) {
                System.out.print(board[i][j] + " | ");
            }
            System.out.println();
            System.out.println("-------------");
        }
    }

    public void startGame() {
        Scanner input = new Scanner(System.in);
        boolean playAgain = true;

        while (playAgain) {
            resetBoard();
            currentMark = 'X';
            boolean gameRunning = true;

            while (gameRunning) {
                displayGrid();
                takeTurn(input);

                if (checkForWinner()) {
                    displayGrid();
                    System.out.println("🎉 Player " + currentMark + " wins!");
                    break;
                }

                if (isBoardFull()) {
                    displayGrid();
                    System.out.println("🤝 It's a tie!");
                    break;
                }

                switchPlayer();
            }

            // Ask if they want to play again
            System.out.print("Would you like to play again? (Y/N): ");
            String answer = input.next().trim().toUpperCase();
            if (!answer.equals("Y")) {
                playAgain = false;
                System.out.println("Thanks for playing Tic Tac Toe!");
            }
        }
    }

    public void takeTurn(Scanner input) {
        int row, col;
        while (true) {
            System.out.println("Player " + currentMark + ", enter your move (row and column: 0, 1, or 2): ");
            System.out.print("Row: ");
            row = input.nextInt();
            System.out.print("Column: ");
            col = input.nextInt();

            if (row >= 0 && row < 3 && col >= 0 && col < 3) {
                if (board[row][col] == ' ') {
                    board[row][col] = currentMark;
                    break;
                } else {
                    System.out.println("That spot is already taken. Try again.");
                }
            } else {
                System.out.println("Invalid input. Please enter values between 0 and 2.");
            }
        }
    }

    public boolean checkForWinner() {
        // Check rows
        for (int i = 0; i < 3; i++) {
            if (board[i][0] != ' ' && board[i][0] == board[i][1] && board[i][1] == board[i][2]) {
                return true;
            }
        }

        // Check columns
        for (int j = 0; j < 3; j++) {
            if (board[0][j] != ' ' && board[0][j] == board[1][j] && board[1][j] == board[2][j]) {
                return true;
            }
        }

        // Check diagonals
        if (board[0][0] != ' ' && board[0][0] == board[1][1] && board[1][1] == board[2][2]) {
            return true;
        }
        if (board[0][2] != ' ' && board[0][2] == board[1][1] && board[1][1] == board[2][0]) {
            return true;
        }

        return false;
    }

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

    private void switchPlayer() {
        currentMark = (currentMark == 'X') ? 'O' : 'X';
    }

    private void resetBoard() {
        for (int i = 0; i < 3; i++) {
            for (int j = 0; j < 3; j++) {
                board[i][j] = ' ';
            }
        }
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






