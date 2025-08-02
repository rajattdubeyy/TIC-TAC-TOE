#include <SFML/Graphics.hpp>
#include <iostream>
#include <cstdlib>  // for rand()
#include <ctime>    // for time()

enum GameState { Welcome, ChooseSymbol, Playing, GameOver };

sf::Font font;
char board[9];
char player = 'X', computer = 'O';
bool playerTurn = true;
GameState state = Welcome;

// Animation variables
float welcomeAlpha = 0.f;
bool welcomeFadeIn = true;

float gameOverAlpha = 0.f;
bool gameOverFadeIn = false;

float symbolScales[9]; // For pop effect when symbol appears

void resetBoard() {
    for (int i = 0; i < 9; i++) {
        board[i] = ' ';
        symbolScales[i] = 0.f;  // Reset scale for animation
    }
    playerTurn = true;
}

bool checkWin(char symbol) {
    int wins[8][3] = {
        {0,1,2},{3,4,5},{6,7,8}, // rows
        {0,3,6},{1,4,7},{2,5,8}, // cols
        {0,4,8},{2,4,6}          // diags
    };
    for (auto &line : wins) {
        if (board[line[0]] == symbol && board[line[1]] == symbol && board[line[2]] == symbol)
            return true;
    }
    return false;
}

bool isTie() {
    for (char c : board) if (c == ' ') return false;
    return true;
}

void computerMove() {
    while (true) {
        int r = rand() % 9;
        if (board[r] == ' ') {
            board[r] = computer;
            symbolScales[r] = 0.f; // Reset scale to animate pop for computer move
            break;
        }
    }
}

void drawBoard(sf::RenderWindow& window) {
    float size = 150;
    for (int i = 0; i < 9; i++) {
        sf::RectangleShape square(sf::Vector2f(size - 5, size - 5));
        square.setPosition((i % 3) * size, (i / 3) * size + 100);
        square.setFillColor(sf::Color::White);
        square.setOutlineColor(sf::Color::Black);
        square.setOutlineThickness(3);
        window.draw(square);

        if (board[i] != ' ') {
            // Animate scale growing from 0 to 1 for pop effect
            if (symbolScales[i] < 1.f)
                symbolScales[i] += 0.05f; // speed of pop animation
            if (symbolScales[i] > 1.f)
                symbolScales[i] = 1.f;

            sf::Text mark;
            mark.setFont(font);
            mark.setString(board[i]);
            mark.setCharacterSize(100);
            mark.setFillColor(sf::Color::Black);

            // Calculate center for scaling
            float centerX = (i % 3) * size + 35 + 50;
            float centerY = (i / 3) * size + 100 + 50;

            mark.setOrigin(50, 50); // center origin for scaling
            mark.setPosition(centerX, centerY);
            mark.setScale(symbolScales[i], symbolScales[i]);

            window.draw(mark);
        }
    }
}

int main() {
    sf::RenderWindow window(sf::VideoMode(450, 600), "Tic Tac Toe");
    window.setFramerateLimit(60);

    if (!font.loadFromFile("arial.ttf")) {
        std::cout << "Font failed to load.\n";
        return 1;
    }

    // Seed random for computer moves
    std::srand(static_cast<unsigned int>(std::time(nullptr)));

    resetBoard();

    while (window.isOpen()) {
        sf::Event event;
        while (window.pollEvent(event))
            if (event.type == sf::Event::Closed)
                window.close();

        // Global restart key to reset game at any time
        if (sf::Keyboard::isKeyPressed(sf::Keyboard::F5)) {
            state = Welcome;
            resetBoard();
            welcomeAlpha = 0.f;
            welcomeFadeIn = true;
            gameOverAlpha = 0.f;
            gameOverFadeIn = false;
            player = 'X';
            computer = 'O';
            playerTurn = true;
        }

        window.clear(sf::Color::Cyan);

        if (state == Welcome) {
            // Animate fade-in for welcome text
            if (welcomeFadeIn) {
                welcomeAlpha += 3.f; // fade speed
                if (welcomeAlpha >= 255.f) {
                    welcomeAlpha = 255.f;
                    welcomeFadeIn = false;
                }
            }

            sf::Text title("Welcome to Tic Tac Toe", font, 32);
            title.setFillColor(sf::Color(0, 0, 0, (sf::Uint8)welcomeAlpha));
            title.setPosition(40, 200);
            window.draw(title);

            sf::Text start("Press ENTER to Start", font, 24);
            start.setFillColor(sf::Color(0, 0, 0, (sf::Uint8)welcomeAlpha));
            start.setPosition(100, 300);
            window.draw(start);

            if (sf::Keyboard::isKeyPressed(sf::Keyboard::Enter))
                state = ChooseSymbol;
        }

        else if (state == ChooseSymbol) {
            sf::Text choose("Choose X or O", font, 32);
            choose.setFillColor(sf::Color::Black);
            choose.setPosition(100, 200);
            window.draw(choose);

            sf::Text xText("Press X for X", font, 24);
            xText.setFillColor(sf::Color::Black);
            xText.setPosition(100, 270);
            window.draw(xText);

            sf::Text oText("Press O for O", font, 24);
            oText.setFillColor(sf::Color::Black);
            oText.setPosition(100, 320);
            window.draw(oText);

            if (sf::Keyboard::isKeyPressed(sf::Keyboard::X)) {
                player = 'X'; computer = 'O'; resetBoard();
                state = Playing;
            }
            if (sf::Keyboard::isKeyPressed(sf::Keyboard::O)) {
                player = 'O'; computer = 'X'; resetBoard();
                state = Playing;
            }
        }

        else if (state == Playing) {
            drawBoard(window);

            if (sf::Mouse::isButtonPressed(sf::Mouse::Left) && playerTurn) {
                sf::Vector2i pos = sf::Mouse::getPosition(window);
                int col = pos.x / 150;
                int row = (pos.y - 100) / 150;
                int idx = row * 3 + col;

                if (row >= 0 && row < 3 && col >= 0 && col < 3 && board[idx] == ' ') {
                    board[idx] = player;
                    symbolScales[idx] = 0.f; // reset pop scale for animation

                    if (checkWin(player) || isTie()) {
                        state = GameOver;
                        gameOverAlpha = 0.f;      // reset fade for game over screen
                        gameOverFadeIn = true;
                    } else {
                        playerTurn = false;
                        computerMove();

                        if (checkWin(computer) || isTie()) {
                            state = GameOver;
                            gameOverAlpha = 0.f;
                            gameOverFadeIn = true;
                        }
                        else
                            playerTurn = true;
                    }
                }
            }
        }

        else if (state == GameOver) {
            drawBoard(window);

            // Animate fade-in for game over message
            if (gameOverFadeIn) {
                gameOverAlpha += 4.f; // fade speed
                if (gameOverAlpha >= 255.f) {
                    gameOverAlpha = 255.f;
                    gameOverFadeIn = false;
                }
            }

            sf::Text msg("", font, 28);
            if (checkWin(player))
                msg.setString("You Won!");
            else if (checkWin(computer))
                msg.setString("You Lost!");
            else
                msg.setString("It's a Tie!");

            msg.setFillColor(sf::Color(0, 0, 0, (sf::Uint8)gameOverAlpha));
            msg.setPosition(150, 560);
            window.draw(msg);

            sf::Text replay("Press R to Restart", font, 20);
            replay.setFillColor(sf::Color(0, 0, 0, (sf::Uint8)gameOverAlpha));
            sf::FloatRect replayBounds = replay.getLocalBounds();
            float xPos = (450.f - replayBounds.width) / 2.f;
            replay.setPosition(xPos, 20);

            window.draw(replay);

            if (sf::Keyboard::isKeyPressed(sf::Keyboard::R)) {
                state = Welcome;
                resetBoard();
                welcomeAlpha = 0.f;
                welcomeFadeIn = true;
            }
        }

        window.display();
    }

    return 0;
}
