# Hàm main

>✏️ [saocodon](https://github.com/saocodon)
>⌛ 18/1/24

Mỗi khi bạn làm việc với một dự án lớn không biết đọc code từ đâu, gợi ý cho bạn là việc đầu tiên là phải đọc hàm `main(int argc, char* argv[])`, vì đó là nơi bắt đầu của code. Các tham số `argc` và `argv` chính là tham số truyền vào khi chạy game từ CLI.

```cpp
#define SDL_MAIN_HANDLED
#include "core/game.hpp"

int main(int argc, char* argv[])
{
    Game* game = new Game();
    game->init("game", SDL_WINDOWPOS_CENTERED, SDL_WINDOWPOS_CENTERED, 800, 600, 0);
    Uint32 frameStart;
    int frameTime;
    while (game->running()) {
        frameStart = SDL_GetTicks();
        game->handleEvents();
        game->update();
        game->render();
        frameTime = SDL_GetTicks() - frameStart;
        if (frameDelay > frameTime)
            SDL_Delay(frameDelay - frameTime);
    }
    game->clean();
    return 0;
}
```

Mình khai báo một con trỏ kiểu `Game` (là gì tí nữa nói sau), nhưng ta sẽ dùng con trỏ này để điều khiển các hoạt động của game, thông qua vòng lặp while vô hạn (cho tới khi người chơi nhấn X, tí nữa nói), thì ta sẽ xử lí sự kiện, update thông số theo từng frame và render ra màn hình. Sau khi ra khỏi vòng while thì ta sẽ dọn dẹp bộ nhớ của game, chống tràn bộ nhớ (memory leak).

Ngoài ra mình còn sử dụng kĩ thuật giới hạn FPS qua hàm [SDL_GetTicks()](https://wiki.libsdl.org/SDL2/SDL_GetTicks) để tính ra khoảng thời gian tối đa của 1 frame, sau đó nếu còn dư thời gian mình sẽ delay nó.