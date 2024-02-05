# Animation và lật sprite

>✏️ [saocodon](https://github.com/saocodon)
>⌛ 5/2/24

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

[animation_system.hpp](https://github.com/Team-BigDy/game/blob/main/core/ecs/animation_system.hpp)
[animation_system.cpp](https://github.com/Team-BigDy/game/blob/main/core/ecs/animation_system.cpp)