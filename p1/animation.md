# Animation và lật sprite

>✏️ [saocodon](https://github.com/saocodon)
>⌛ 18/1/24

Bài này sẽ ngắn thôi, vì animation chỉ là sự tiến hoá của component `SpriteComponent`.

Bản chất là `SpriteComponent` chứa một `SDL_Texture*` có chứa hình ảnh ta đã nạp vào thông qua `TextureManager`, vì vậy khi chạy nó sẽ tạo ra một hình ảnh trên màn hình. Bây giờ, nếu muốn nhân vật chuyển động, ta cần tạo ra một vài frame cho nó, sau đó lặp đi lặp lại. Ở đây mình cho thẳng

```cpp
struct SpriteComponent {
	SDL_Texture* texture;
	std::vector<SDL_Rect> animRects; // cai nay nay
	// frameCount = animRects.size()
	int frameIndex, animSpeed; // cai nay
	bool animated; // cai nay
	SDL_RendererFlip flips; // cai nay
};
```
- Biến:
	- `vector<SDL_Rect> animRects` dùng để chọn sẵn các selection của frame để sử dụng trong hàm [SDL_RenderCopy](https://wiki.libsdl.org/SDL2/SDL_RenderCopy) và [SDL_RenderCopyEx](https://wiki.libsdl.org/SDL2/SDL_RenderCopyEx).
	- `frameIndex` coi như biến `i` mỗi khi lặp, `animSpeed` là một đại lượng nhanh chậm thôi, chứ nó không thật sự là speed lắm =))
	- `animated`: nếu bằng `true`, cho phép animate, nếu `false` thì đứng im.
	- `SDL_RendererFlip flips`: một biến nguyên cho phép tuỳ chỉnh flags, cụ thể là khi nhân vật di chuyển theo hướng bên trái mình muốn lật cái sprite lại.

```cpp
#pragma once

// system_manager has already been included in 'coordinator.hpp'
#include "../core/components.hpp"
#include "../core/coordinator.hpp"
#include "keyboard_system.hpp"
#include "../core/state.hpp"
#include <SDL.h>

extern Coordinator game_manager;

class AnimationSystem : public System {

public:
	void update() {
		for (auto const& e : entities) {
			auto& sprites = game_manager.getComponent<SpriteComponent>(e);
			auto& transform = game_manager.getComponent<TransformComponent>(e);

			// finally check if player has moved
			if (transform.velocity.x != 0 || transform.velocity.y != 0) sprites.animated = true;
			else {
				sprites.animated = false;
				sprites.frameIndex = 0;
			}

			if (sprites.animated) {
				sprites.frameIndex = SDL_GetTicks() / sprites.animSpeed % sprites.animRects.size();
			}

			if (transform.velocity.x < 0)
				sprites.flips = SDL_FLIP_HORIZONTAL;
			if (transform.velocity.x > 0)
				sprites.flips = SDL_FLIP_NONE;
			// if (transform.velocity.y < 0)
			// if (transform.velocity.y > 0) // TODO
		}
	}
	void render(SDL_Renderer* ren) {
		for (auto const& e : entities) {
			auto& sprites = game_manager.getComponent<SpriteComponent>(e);
			auto& transform = game_manager.getComponent<TransformComponent>(e);
			SDL_Rect srcRect = sprites.animRects[sprites.frameIndex];
			SDL_Rect destRect;
			destRect.x = transform.position.x;
			destRect.y = transform.position.y;
			destRect.w = srcRect.w / 2;
			destRect.h = srcRect.h / 2;
			SDL_RenderCopyEx(ren, sprites.texture, &srcRect, &destRect, NULL, NULL, sprites.flips);
		}
	}
};
```