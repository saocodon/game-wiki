# Entity-Component-System (ECS)

>✏️ [saocodon](https://github.com/saocodon)
>⌛ 5/2/24

>Đây sẽ là phần khó nhất của code. Chúc may mắn =))
>[cynmeiciel](https://github.com/cynmeiciel) đã góp ý rằng nên tách các lớp ra thành 2 file `*.cpp` và `*.hpp` nên mình đã viết lại chỗ code này.

## Nếu nó khó vậy, tại sao lại sinh ra cái của nợ này?
Đều là chất xám con người cả đấy. Giả sử mình có một class `Human` -> class `Player`, `Villager` và có một class `NonHuman` -> class `Monster` chẳng hạn (`->` tạm thời kí hiệu là thừa kế). Bây giờ nếu mình muốn tạo ra một con zombie (class `Zombie`) thì rõ ràng là zombie vừa là người vừa không phải người, nhìn cái cây thừa kế đã thấy lỏ lỏ rồi =))

<p align="center">
  <img src="img/1.png" />
</p>

Giải pháp sinh ra là thay vì tạo từng class cho mỗi thuộc tính như vậy, ta thay thành một bitset, giả sử nếu bit 0 đại diện cho người, bit 1 đại diện cho quỷ, thì con zombie sẽ có cả hai bit đó bật lên. Tiện lợi, right? :))

Ok, nếu bạn đã nói vậy, thì bây giờ quản lí mớ dữ liệu này thì sao? Vì nếu ta không chia class, không có chỗ nào để chứa dữ liệu cả. Tuy nhiên, quan sát kĩ rằng khi ta biến đống class này thành bitset, lúc này mỗi entity trong game được coi là độc lập nhau, bởi vì nó chẳng còn mối quan hệ thừa kế hay class hay gì nữa cả. Vậy, ta sẽ gán cho mỗi entity một cái ID để quản lý từng object một, sau đó ta sẽ có một cấu trúc dữ liệu nào đó giống như cái hộc tủ để lưu dữ liệu của từng entity một vào trong đó, bất chấp kiểu dữ liệu (từ `int`, `float`... đến cả các class tự tạo - component).

Một vấn đề đã được giải quyết. Giờ làm sao để quản lí mớ entity và component này? Câu trả lời là ta sẽ có một hệ thống nào đó, mỗi hệ thống sẽ quản lí một hoặc một vài thuộc tính của một entity. Và nó cũng sẽ có danh sách những entity thuộc quyền quản lí của nó, để biết đường xử lí. Cứ mỗi lần update hay render, nó chỉ cần loop một lần qua mớ entity đó của nó, thay đổi dữ liệu component (ta sẽ dùng một class nào đó để quản lí phần này), thêm xoá sửa dữ liệu, hoạt động trơn tru. Đó chính là lí do vì sao ra đời hệ thống Entity-Component-System (ghép ba đứa lại với nhau).

## Nghe hay đấy, giờ làm sao thực hiện hoá?
### Entity
Ta xử lí từng em một, em Entity trước. Như đã nói, entity chỉ là một cái ID, vì thế mình alias lại cho dễ dùng:

```cpp
#define entity std::uint32_t

const int MAX_ENTITIES = 5000;
```

Bây giờ ta phải làm một cái bitset như đã nói ở trên, bitset này ngành game người ta gọi nó là *signature* (bảng đánh dấu, nghe hợp lí phết).

```cpp
const int MAX_COMPONENTS = 32;

#define Signature std::bitset<MAX_COMPONENTS>
```

Một entity chủ yếu chỉ có hai thuộc tính đó, vì vậy bây giờ ta sẽ tạo một class manager cho nó.

[entity_manager.hpp](https://github.com/Team-BigDy/game/blob/main/core/ecs/entity_manager.hpp)
[entity_manager.cpp](https://github.com/Team-BigDy/game/blob/main/core/ecs/entity_manager.cpp)

- Giải thích code sương sương:
	- `EntityManager()`: Đầu tiên, khi khởi tạo manager mình sẽ tạo một cái queue, lúc đầu mình sẽ thêm toàn bộ 5000 cái ID vào đó, là 5000 cái ID này coi như khả dụng, chưa có cái entity nào xài. Với mỗi entity tạo ra mình sẽ lấy cái ID ở đầu queue đặt cho nó, coi như là ID đó đã được xài, mình xoá cái ID đó ra khỏi queue. Rồi tới lúc một entity nào đó bị xoá mình lại thêm nó vào cái queue này, cứ vậy cho tới chừng nào vượt quá `MAX_ENTITIES` thì mình sẽ báo lỗi bằng hàm `assert()`.
	- `createEntity()`: trả về 1 ID cho entity mới. Nếu đã chạm mốc `MAX_ENTITIES` thì báo lỗi.
	- `destroyEntity(e)`: xoá entity đi. Nếu `e > MAX_ENTITIES` thì `e` không tồn tại -> báo lỗi.
	- `setSignature(e, signature)`: Đặt signature của entity `e` là `signature`.
	- `getSignature(e)`: Lấy signature của `e`, lập luận tương tự như `destroyEntity(e)`.

### Mảng Component
- Có 5 điều cần lưu ý:
	- Đây chỉ là một cái mảng thông thường, nhưng nó có thể chứa kiểu dữ liệu nào cũng được (vì nó sẽ chứa các component ta tạo ra, thường là struct hoặc class), vì vậy ta sẽ dùng `template<typename T>` cho nó.
	- Để đỡ tốn RAM đồng thời tăng tốc độ chạy, ta sẽ đảm bảo sao cho mảng luôn liên tục (không có lỗ hổng giữa các phần tử) -> ta đỡ tốn một dòng `if (valid)`. Để được vậy, mỗi khi ta xoá một entity mà không phải entity cuối cùng (entity mới nhất được thêm vào), thì ta sẽ xách cái entity cuối cùng lên vứt vào cái lỗ hổng đó, đồng thời dùng một `map` để lưu lại index dữ liệu của các entity.
	- Đã là mảng, thì nó cũng sẽ có các hàm `insertData(entity e, T component)`, `removeData(entity e)`, `getData(entity e)`.
	- Mỗi khi xoá phần tử (gọi `removeData()`), ta cũng phải cập nhật lại map, vì vậy ta sẽ có một hàm sự kiện `entityDestroyed(entity e)` để biết đường cập nhật map. Ta sẽ gọi nó mỗi khi xoá phần tử.
	- Vì mảng này là một mảng đặc biệt (nó có các tính năng đặc biệt kể trên), ta sẽ tách nó ra thành một class (gọi là `ComponentArray`), và vì ta không thể biết được số lượng `ComponentArray` đang tồn tại, nó phụ thuộc vào số component ta tạo ra (nhưng chẳng lẽ mỗi lần tạo một component là quay vào cái code này sửa lại?), vì thế ta sẽ tạo một lớp cơ sở trừu tượng `IComponentArray`. Lớp này sẽ được thừa kế bởi `ComponentArray`, ta sẽ không dùng tới lớp này để khai báo đối tượng mà chỉ dùng để điều khiển đám `ComponentArray` thôi.
- Chi tiết các bạn có thể đọc tại [đây](https://austinmorlan.com/posts/entity_component_system/).

[component_array.hpp](https://github.com/Team-BigDy/game/blob/main/core/ecs/component_array.hpp)
[component_array.cpp](https://github.com/Team-BigDy/game/blob/main/core/ecs/component_array.cpp)

### Component Manager
- Xong cái đám array component đó rồi, giờ ta cần phải quản lí nó. Để việc quản lí thuận tiện ta lại tạo thêm một class mới làm manager. Nó sẽ có các nhiệm vụ như sau:
	- `registerComponent()<T>`: đăng kí component mới với hệ thống ECS, để nó biết đường tạo sẵn một `ComponentArray<T>` dùng thêm bớt dữ liệu. Hàm này sẽ chỉ cập nhật bảng đăng kí thôi, nó sẽ không làm gì cả. Tác dụng của nó sẽ được phát huy khi gọi các hàm `addComponent(e, component)`, `removeComponent(e)`, `getComponent(e)`. Cụ thể, nó sẽ thêm 1 key và 1 ID vào `componentTypes`, thêm 1 key và 1 con trỏ kiểu `ComponentArray<T>` vào `componentArrays`.
	- `getComponentArray()` trả về một con trỏ `shared_ptr` kiểu `ComponentArray<T>`. Nó sẽ lấy mã của `T`, kiểm tra xem đã đăng kí chưa, nếu chưa thì sẽ `assert` ngay tại đó, nếu rồi sẽ ép các con trỏ `shared_ptr<IComponentArray>` trở thành kiểu `shared_ptr<ComponentArray<T>>` rồi trả về một con trỏ để thay đổi dữ liệu. Nhờ đó mà các hàm `addComponent(e, component)`, `removeComponent(e)`, `getComponent(e)` có thể hoạt động được.
	- `EntityDestroyed()` sẽ được gọi khi một entity bị xoá và sẽ được thông báo cho tất cả các manager biết thông qua một hệ thống khác, tạm gọi là `Coordinator`.
- Về mặt tài nguyên, class có:
	- Một map để chứa key: mã `T`, value: ID component.
	- Một map khác chứa key: mã `T`, value: con trỏ trỏ đến `ComponentArray<T>`.
- Một số từ khoá: `std::make_shared`, `std::static_pointer_cast`.

[component_manager.hpp](https://github.com/Team-BigDy/game/blob/main/core/ecs/component_manager.hpp)
[component_manager.cpp](https://github.com/Team-BigDy/game/blob/main/core/ecs/component_manager.cpp)

### System và System Manager
Gần xong rồi, cố lên.

Bây giờ ta đã có được entity và component, được quản lí đầy đủ thông qua `EntityManager` và `ComponentManager`. Bây giờ ta cần một hệ thống làm việc với hai đứa này, đó chính là `System`. Lớp `System` mới sẽ là một lớp cơ sở trừu tượng để các system ta tự tạo sau này sẽ dẫn xuất từ lớp này. Và đã nói như vậy, nghĩa là sẽ có nhiều system, mà đã có nhiều system thì sẽ phải có một `SystemManager` để quản lí đống system này. Có vẻ bạn đang dần quen quy luật rồi đấy =))

- `SystemManager` sẽ giống `ComponentManager` ở vài chỗ, vì nó cũng làm việc với những đứa con của nó thông qua con trỏ `shared_ptr<T>` (do các system ta sắp tạo cũng là một class), vì vậy nó cũng có `registerSystem()`, `entityDestroyed(entity e)` (thông báo cho các system kia biết -> xoá entity ra khỏi toàn bộ các system, vì các system chỉ quản lí một/một số component -> nhiều system). Nhưng nó sẽ có thêm hai hàm mới:
	- `SetSignature(Signature signature)`: vì một system chỉ quản lí một vài component (tương đương một vài bit trong bitset), nên các system cũng có signature riêng, với bit 1 thì nó sẽ quản lí component đó, bit 0 thì không quản lí.
	- `EntitySignatureChanged(entity e, Signature entitySignature)`: khi một entity thay đổi signature (mất/thêm một vài component), có thể một số system sẽ không quản lí nó nữa, có thể một số system mới sẽ bắt đầu quản lí nó. Hàm này sẽ loop qua toàn bộ các system, kiểm tra rằng nếu `entitySignature & systemSignature == systemSignature` (`systemSignature < entitySignature` là cái chắc, vì nó chỉ quản lí một số component, chắc chắn rằng entity có nhiều component hơn như vậy). Nếu điều kiện đúng, system giữ lại entity, sai thì nó xoá entity ra khỏi danh sách của nó.

[system_manager.hpp](https://github.com/Team-BigDy/game/blob/main/core/ecs/system_manager.hpp)
[system_manager.cpp](https://github.com/Team-BigDy/game/blob/main/core/ecs/system_manager.cpp)

### Bước cuối cùng
Ba hệ thống Entity-Component-System đã hoàn thành, nhưng vấn đề là nó chưa thể giao tiếp với nhau được. Ta cần chúng hoạt động cùng nhau để quản lí dữ liệu, vì thế ta tạo thêm một class nữa gọi là `Coordinator` (hết tên đặt :)))

Class này cũng dễ nên chắc mình không cần giải thích đâu nhỉ.

[coordinator.hpp](https://github.com/Team-BigDy/game/blob/main/core/ecs/coordinator.hpp)
[coordinator.cpp](https://github.com/Team-BigDy/game/blob/main/core/ecs/coordinator.cpp)

Đến đây, vì một số lý do liên quan đến hàm template, nên mình tách riêng một file [coordinator.inl](https://github.com/Team-BigDy/game/blob/main/core/ecs/coordinator.inl) chỉ để chứa các hàm có template:

Sau đó thêm 1 dòng cuối ở `coordinator.hpp`:
```cpp
// ...

#include "coordinator.inl"
```

## Ngon, có ECS rồi, giờ xài sao?
Đây là lí do bạn thấy ở `game.hpp` mình có `entity player` (for testing purposes only :)).

Để dùng được thì dĩ nhiên, bạn cần có cả entity, component và system :)) entity ta có rồi, component mình đặt đại hai cái struct `TransformComponent` (quản lí vị trí, tốc độ) và `SpriteComponent` (quản lí sprite).

Trong này có một số cái liên quan tới animation, tạm thời bỏ qua nhé.

```cpp
#pragma once

#include <SDL.h>
#include <vector>
#include "vec.hpp"

struct SpriteComponent {
	SDL_Texture* texture;
	std::vector<SDL_Rect> animRects; // cai nay nay
	// frameCount = animRects.size()
	int frameIndex, animSpeed; // cai nay
	bool animated; // cai nay
	SDL_RendererFlip flips; // cai nay
};


struct TransformComponent {
	Vec position, velocity;
};
```

Rồi đến system, mình có một system là để quản lí animation (code cũng không quá khó hiểu) và một system khác để quản lí vị trí (movement). `AnimationSystem` là để quản lí sprites, animation, render; `MovementSystem` quản lí vị trí. Cả hai đều có hàm `update()`, nhưng `render()` thì để `AnimationSystem` lo, ta chỉ cần đặt các hàm này vào đúng chỗ trong class `Game` là được.

[animation_system.hpp](https://github.com/Team-BigDy/game/blob/main/sys/animation_system.hpp)
[movement_system.hpp](https://github.com/Team-BigDy/game/blob/main/sys/movement_system.hpp)

Bây giờ xem cách sử dụng nhé:

Khai báo `game_manager` ở `game.cpp`, các file `.hpp` còn lại ta dùng `extern`. Có một số dòng liên quan đến lật sprite thì các bạn tham khảo ở [đây](animation.md) nhé.

```cpp
Coordinator game_manager;

void Game::init(const char* title, int x, int y, int w, int h, int flags) {
	// Do work here
	game_manager.init();
	game_manager.registerComponent<TransformComponent>();
	game_manager.registerComponent<SpriteComponent>();
	movementSystem = game_manager.registerSystem<MovementSystem>();
	animationSystem = game_manager.registerSystem<AnimationSystem>();
	keyboardSystem = game_manager.registerSystem<KeyboardSystem>();
	Signature signature;

	// include components into systems
	signature.set(game_manager.getComponentType<TransformComponent>());
	signature.set(game_manager.getComponentType<SpriteComponent>());
	game_manager.setSystemSignature<AnimationSystem>(signature);
	signature.reset(game_manager.getComponentType<SpriteComponent>());
	game_manager.setSystemSignature<MovementSystem>(signature);

	// get ID for player
	player = game_manager.createEntity();
	// TODO adapt Sprite component
	SDL_Rect dstR; dstR.w = dstR.h = 64; dstR.x = dstR.y = 50;
	SDL_Texture* t = TextureManager::LoadTexture("assets/player2.png", renderer);
	std::vector<SDL_Rect> animRects = {
		SDL_Rect{ 0, 0, 85, 169 },
		SDL_Rect{ 91, 0, 90, 169 },
		SDL_Rect{ 187, 0, 97, 169 },
		SDL_Rect{ 290, 0, 108, 169 },
		SDL_Rect{ 404, 0, 96, 175 }
	};
	game_manager.addComponent(player, SpriteComponent{ t, animRects, 0, 130, true, SDL_FLIP_NONE });
	game_manager.addComponent(player, TransformComponent{ Vec(50, 50), Vec(0, 0) });
}

void Game::update() {
	animationSystem->update();
	keyboardSystem->update(ev);
	movementSystem->update(ev);
}

void Game::render() {
	SDL_RenderClear(renderer);
	// this is where we would add stuff to render
	animationSystem->render(renderer);
	SDL_RenderPresent(renderer);
}
```

- Về cơ bản:
	- Ta khởi tạo `game_manager`.
	- Đăng kí các component của mình.
	- Đăng kí system.
	- Đặt signature cho system.
	- Khởi tạo entity và dữ liệu.
	- Dùng `addComponent` để thêm dữ liệu vào entity.

Voilà, xong rồi!