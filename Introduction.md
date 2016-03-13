# Introduction #

This is a small code snippet that allows accurate collision detection in Ogre without any external physics library. It's based on the MOC (minimal ogre collision) lib idea, but with focus on performances and efficiency.

# Details #

**Features:**

  * Accurate collision that is not based on bounding box.
  * collision detection type for different entities (bounding box, sphere, or accurate). for example, if you have a simple entity like a box or a sphere you can just use bounding box check for this specific entity instead of using accurate collision.
  * static geometry don't query all entities - query only entities you register to the collision tools. still support ogre's query flags.
  * ignore-entity pointer, so entities won't check collision with themselves.
  * first-hit optimization, that stop checking once a positive check is found instead of returning the nearest collision (good if you only want to check collision and don't care about nearest target)

**Performance:**

for performance test I ran a scene with ~825 static entities, all with accurate collision type, and some NPCs running around randomly.
every running NPC is another collision test per frame. I always rendered the same scene from the same camera, and with const number of rendered NPCs.

The only changing factor for the tests was how many of the NPCs are running and doing collision checks, while the rest simply stand still and don't do collision. here are the results:
```
0 NPCs moving: 274 FPS
50 NPCs moving: still 274 FPS
100 NPCs moving: 188 FPS
200 NPCs moving: 102 FPS
```

**Caveat:**
  * don't support terrains (didn't need so excluded that), unless are converted to an entity with mesh.
  * the collision check ignore ogre query flag types (billboard, terrain, entity...) but query flags are supported. personally I find this an advantage.
  * no skeleton-based animation supported (but node movement / rotation / scaling is supported)

**How to use:**

  * create a collision tools instance
  * register every entity you want to be able to collide with (you choose collision type and register entities either as static or dynamic)
  * note: for accurate collisions, the tools will hold a cache of the vertices and indices of every mesh type. meshes are removed from cache once there are no more references to them.
  * to check collision there are two main functions: one with starting point and ending point, and one with just a ray.

**Checking collision example:**

`Collision::SCheckCollisionAnswer ret = collision_tools.check_ray_collision(FromPos?, ToPos?, 0.3f, 0.5f, (QUERY_OBJECT_ENTITY | QUERY_TERRAIN_ENTITY), this->m_Entity, true);`

**Registering entities for collision example:**

`collision_tools.register_static_entity(lEntity, Position, quat, Size, Collision::COLLISION_BOX); collision_tools.register_entity(lEntity, Collision::COLLISION_ACCURATE);`

**Static batch:**

If you are using ogre's static batch and you want to collide with it, you need to add each of the entities in the static batch using 'register\_static\_entity()'.

You don't need to register the batch itself.