# CIS407-WEEK2-GUESSING-GAME
Java guessing game 1-100



import java.util.Random;

public class Game {
    // --- Data fields ---
    private int number;        // Random number to be guessed
    private int guessNumber;   // Current guess
    private int counter;       // Number of guesses made

    // --- Methods ---

    // Generates a random number between 1 and 100
    public void generateNumberToBeGuessed() {
        Random rand = new Random();
        number = rand.nextInt(100) + 1;
        counter = 0; // Reset counter for a new game
    }

    // Accepts the user's guess and increments counter
    public void makeGuess(int guess) {
        guessNumber = guess;
        counter++;
    }

    // Returns true if the guess is correct
    public boolean isCorrectGuess() {
        return guessNumber == number;
    }

    // Display welcome message
    public void displayWelcomeMessage() {
        System.out.println("Welcome to the Guessing Game!");
        System.out.println("Try to guess the number between 1 and 100.\n");
    }

    // Display prompt message
    public void displayPleaseGuessMessage() {
        System.out.println("Please enter your guess (1–100):");
    }

    // Display message for correct guess
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

    // Display hints based on how close the guess is
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


//---------------------------------------------------------


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

