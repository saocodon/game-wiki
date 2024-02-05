# Hello World!

Chào mừng bạn, tân coder đã đến với nhóm tụi mình.

Đây là một trang wiki tụi mình lập ra nhằm giải quyết thắc mắc dành cho các bạn tân coder mới tham gia [dự án của chúng mình](https://github.com/Team-BigDy/game). Chúng mình là một nhóm nhỏ, với hai thành viên là chủ chốt của dự án này.

Dự án này sẽ là một game nhập vai (RPG) nhỏ, đồ hoạ 2D, được viết hoàn toàn bằng C++ cùng thư viện [SDL2](https://github.com/libsdl-org/SDL/releases) mà không dùng bất cứ phần mềm làm game nào. Là những người trẻ thơ dại, chúng mình cũng là tay mơ trong ngành game gủng, nhưng cũng nuôi hi vọng rằng đây sẽ là một trải nghiệm quý giá với tất cả những ai tham gia.

Dự án này sẽ không phải trọng tâm của chúng mình, chỉ đơn giản là sở thích, vì còn vướng bận nhiều thứ nên tần suất code dự án này sẽ không nhiều. Trong quá trình chỉnh sửa tất nhiên không thể tránh khỏi sai sót, rất mong nhận được phản hồi từ các bạn!

Code này được tham khảo từ [series này](https://www.youtube.com/playlist?list=PLhfAbcv9cehhkG7ZQK0nfIGJC_C-wSLrx) và [blog này](https://austinmorlan.com/posts/entity_component_system/).

---

- [Cài đặt môi trường](setup.md)

### Phần 1: Engine
- [Hàm main](p1/main-function.md)
- [Lớp Game và tổng quan code](p1/game.md)
- [Lớp TextureManager](https://github.com/Team-BigDy/game/blob/main/core/texture_manager.hpp)
- [Hệ thống quản lí dữ liệu Entity-Component-System (ECS)](p1/ecs.md)
- [Xử lí bàn phím](p1/keyboard.md)
- [Phát hiện va chạm](p1/collision.md)
- [Chia nhóm cho entity](p1/entity-grouping.md)
- [Animation và lật sprite](p1/animation.md)
- [Camera](p1/camera.md)
- [Quản lý asset](p1/asset-manager.md)
- [Xử lý chữ trên màn hình](p1/sdl-ttf.md)

### Phần 2: Gameplay
Coming soon...

---

**README**

*saocodon* (5/2/24):

Sau commit mới nhất về việc tách lớp thành hai file `.cpp` và `.hpp`, mình lại gặp một vấn đề về linker error:

```
Build started at 15:44...
1>------ Build started: Project: MySDLGame, Configuration: Debug x64 ------
1>component_manager.cpp
1>coordinator.cpp
1>system_manager.cpp
1>game.cpp
1>main.cpp
1>animation_system.cpp
1>C:\MySDLGame\sys\animation_system.cpp(36,34): warning C4244: '=': conversion from 'float' to 'int', possible loss of data
1>C:\MySDLGame\sys\animation_system.cpp(37,34): warning C4244: '=': conversion from 'float' to 'int', possible loss of data
1>keyboard_system.hpp
1>movement_system.cpp
1>C:\MySDLGame\sys\movement_system.cpp(12,50): warning C4244: '=': conversion from 'double' to 'float', possible loss of data
1>C:\MySDLGame\sys\movement_system.cpp(13,53): warning C4244: '+=': conversion from 'double' to 'float', possible loss of data
1>C:\MySDLGame\sys\movement_system.cpp(17,52): warning C4244: '=': conversion from 'double' to 'float', possible loss of data
1>C:\MySDLGame\sys\movement_system.cpp(18,54): warning C4244: '+=': conversion from 'double' to 'float', possible loss of data
1>Generating Code...
1>x64\Debug\keyboard_system.obj : warning LNK4042: object specified more than once; extras ignored
1>game.obj : error LNK2019: unresolved external symbol "public: void __cdecl KeyboardSystem::update(union SDL_Event)" (?update@KeyboardSystem@@QEAAXTSDL_Event@@@Z) referenced in function "public: void __cdecl Game::update(void)" (?update@Game@@QEAAXXZ)
1>game.obj : error LNK2019: unresolved external symbol "public: static struct SDL_Texture * __cdecl TextureManager::LoadTexture(char const *,struct SDL_Renderer *)" (?LoadTexture@TextureManager@@SAPEAUSDL_Texture@@PEBDPEAUSDL_Renderer@@@Z) referenced in function "public: void __cdecl Game::init(char const *,int,int,int,int,int)" (?init@Game@@QEAAXPEBDHHHHH@Z)
1>game.obj : error LNK2019: unresolved external symbol "public: void __cdecl ComponentManager::registerComponent<struct TransformComponent>(void)" (??$registerComponent@UTransformComponent@@@ComponentManager@@QEAAXXZ) referenced in function "public: void __cdecl Coordinator::registerComponent<struct TransformComponent>(void)" (??$registerComponent@UTransformComponent@@@Coordinator@@QEAAXXZ)
1>game.obj : error LNK2019: unresolved external symbol "public: void __cdecl ComponentManager::registerComponent<struct SpriteComponent>(void)" (??$registerComponent@USpriteComponent@@@ComponentManager@@QEAAXXZ) referenced in function "public: void __cdecl Coordinator::registerComponent<struct SpriteComponent>(void)" (??$registerComponent@USpriteComponent@@@Coordinator@@QEAAXXZ)
1>game.obj : error LNK2019: unresolved external symbol "public: class std::shared_ptr<class MovementSystem> __cdecl SystemManager::registerSystem<class MovementSystem>(void)" (??$registerSystem@VMovementSystem@@@SystemManager@@QEAA?AV?$shared_ptr@VMovementSystem@@@std@@XZ) referenced in function "public: class std::shared_ptr<class MovementSystem> __cdecl Coordinator::registerSystem<class MovementSystem>(void)" (??$registerSystem@VMovementSystem@@@Coordinator@@QEAA?AV?$shared_ptr@VMovementSystem@@@std@@XZ)
1>game.obj : error LNK2019: unresolved external symbol "public: class std::shared_ptr<class AnimationSystem> __cdecl SystemManager::registerSystem<class AnimationSystem>(void)" (??$registerSystem@VAnimationSystem@@@SystemManager@@QEAA?AV?$shared_ptr@VAnimationSystem@@@std@@XZ) referenced in function "public: class std::shared_ptr<class AnimationSystem> __cdecl Coordinator::registerSystem<class AnimationSystem>(void)" (??$registerSystem@VAnimationSystem@@@Coordinator@@QEAA?AV?$shared_ptr@VAnimationSystem@@@std@@XZ)
1>game.obj : error LNK2019: unresolved external symbol "public: class std::shared_ptr<class KeyboardSystem> __cdecl SystemManager::registerSystem<class KeyboardSystem>(void)" (??$registerSystem@VKeyboardSystem@@@SystemManager@@QEAA?AV?$shared_ptr@VKeyboardSystem@@@std@@XZ) referenced in function "public: class std::shared_ptr<class KeyboardSystem> __cdecl Coordinator::registerSystem<class KeyboardSystem>(void)" (??$registerSystem@VKeyboardSystem@@@Coordinator@@QEAA?AV?$shared_ptr@VKeyboardSystem@@@std@@XZ)
1>game.obj : error LNK2019: unresolved external symbol "public: unsigned char __cdecl ComponentManager::getComponentType<struct TransformComponent>(void)" (??$getComponentType@UTransformComponent@@@ComponentManager@@QEAAEXZ) referenced in function "public: void __cdecl Coordinator::addComponent<struct TransformComponent>(unsigned int,struct TransformComponent)" (??$addComponent@UTransformComponent@@@Coordinator@@QEAAXIUTransformComponent@@@Z)
1>game.obj : error LNK2019: unresolved external symbol "public: unsigned char __cdecl ComponentManager::getComponentType<struct SpriteComponent>(void)" (??$getComponentType@USpriteComponent@@@ComponentManager@@QEAAEXZ) referenced in function "public: void __cdecl Coordinator::addComponent<struct SpriteComponent>(unsigned int,struct SpriteComponent)" (??$addComponent@USpriteComponent@@@Coordinator@@QEAAXIUSpriteComponent@@@Z)
1>game.obj : error LNK2019: unresolved external symbol "public: void __cdecl SystemManager::setSignature<class AnimationSystem>(class std::bitset<32>)" (??$setSignature@VAnimationSystem@@@SystemManager@@QEAAXV?$bitset@$0CA@@std@@@Z) referenced in function "public: void __cdecl Coordinator::setSystemSignature<class AnimationSystem>(class std::bitset<32>)" (??$setSystemSignature@VAnimationSystem@@@Coordinator@@QEAAXV?$bitset@$0CA@@std@@@Z)
1>game.obj : error LNK2019: unresolved external symbol "public: void __cdecl SystemManager::setSignature<class MovementSystem>(class std::bitset<32>)" (??$setSignature@VMovementSystem@@@SystemManager@@QEAAXV?$bitset@$0CA@@std@@@Z) referenced in function "public: void __cdecl Coordinator::setSystemSignature<class MovementSystem>(class std::bitset<32>)" (??$setSystemSignature@VMovementSystem@@@Coordinator@@QEAAXV?$bitset@$0CA@@std@@@Z)
1>game.obj : error LNK2019: unresolved external symbol "public: void __cdecl ComponentManager::addComponent<struct SpriteComponent>(unsigned int,struct SpriteComponent)" (??$addComponent@USpriteComponent@@@ComponentManager@@QEAAXIUSpriteComponent@@@Z) referenced in function "public: void __cdecl Coordinator::addComponent<struct SpriteComponent>(unsigned int,struct SpriteComponent)" (??$addComponent@USpriteComponent@@@Coordinator@@QEAAXIUSpriteComponent@@@Z)
1>game.obj : error LNK2019: unresolved external symbol "public: void __cdecl ComponentManager::addComponent<struct TransformComponent>(unsigned int,struct TransformComponent)" (??$addComponent@UTransformComponent@@@ComponentManager@@QEAAXIUTransformComponent@@@Z) referenced in function "public: void __cdecl Coordinator::addComponent<struct TransformComponent>(unsigned int,struct TransformComponent)" (??$addComponent@UTransformComponent@@@Coordinator@@QEAAXIUTransformComponent@@@Z)
1>animation_system.obj : error LNK2019: unresolved external symbol "public: struct SpriteComponent & __cdecl ComponentManager::getComponent<struct SpriteComponent>(unsigned int)" (??$getComponent@USpriteComponent@@@ComponentManager@@QEAAAEAUSpriteComponent@@I@Z) referenced in function "public: struct SpriteComponent & __cdecl Coordinator::getComponent<struct SpriteComponent>(unsigned int)" (??$getComponent@USpriteComponent@@@Coordinator@@QEAAAEAUSpriteComponent@@I@Z)
1>animation_system.obj : error LNK2019: unresolved external symbol "public: struct TransformComponent & __cdecl ComponentManager::getComponent<struct TransformComponent>(unsigned int)" (??$getComponent@UTransformComponent@@@ComponentManager@@QEAAAEAUTransformComponent@@I@Z) referenced in function "public: struct TransformComponent & __cdecl Coordinator::getComponent<struct TransformComponent>(unsigned int)" (??$getComponent@UTransformComponent@@@Coordinator@@QEAAAEAUTransformComponent@@I@Z)
1>movement_system.obj : error LNK2001: unresolved external symbol "public: struct TransformComponent & __cdecl ComponentManager::getComponent<struct TransformComponent>(unsigned int)" (??$getComponent@UTransformComponent@@@ComponentManager@@QEAAAEAUTransformComponent@@I@Z)
1>C:\MySDLGame\x64\Debug\MySDLGame.exe : fatal error LNK1120: 15 unresolved externals
1>Done building project "MySDLGame.vcxproj" -- FAILED.
========== Build: 0 succeeded, 1 failed, 0 up-to-date, 0 skipped ==========
========== Build completed at 15:44 and took 06.958 seconds ==========
```