# Physics Sandbox

A 3D physics sandbox in a single self-contained HTML file. It uses Three.js for rendering and Rapier (WASM) for physics. There's no build step: open `index.html` in a modern browser. Three.js, Rapier and lil-gui load from a CDN, so you need an internet connection the first time. Everything else is generated procedurally at runtime: terrain, textures, models and sounds.

## Running

```bash
# any static server works; opening the file directly also works in Chrome/Edge/Firefox
python3 -m http.server 8000   # then open http://localhost:8000
```

## What's inside

- **World**
  - 500 m procedural terrain with a flat build zone, a lake, a cliff, a long incline for ramp tests, and forest, desert, snow and beach biomes.
  - Roads, a minecart rail line, and a stunt park with ramps, jumps and a loop.
  - Trees you can chop and rocks you can mine.
  - Day/night cycle, fog, soft shadows, and weather (rain, snow, storms, wind).
- **Test Arena**: a walled 20 × 20 m checkerboard with a measuring grid. It starts with:
  - a 100-box pyramid, random bodies, a ragdoll and a crash-test dummy with a damage meter;
  - a hanging chain, a Newton's cradle, a ramp, a see-saw and a rotating paddle.
- **Physics**
  - Fixed 1/120 s timestep with an accumulator, and CCD for fast bodies.
  - Extra solver iterations on jointed bodies, and optional substeps.
  - Per-material friction (static and kinetic), restitution, density and breakage strength.
  - Rolling resistance, sticky surfaces, sleeping, and gyroscopic precession.
  - Breakable joints and Voronoi fracture.
  - Buoyancy and particle water, with pumps, pipes and valves.
  - Wind and aerodynamics: wings, gliders, kites, parachutes and sails.
  - Soft bodies: cloth, jelly and rope bridges.
  - Fire and heat, electricity, pneumatics, magnets, and a gravity gun.
  - Explosions and disasters: earthquake, tornado, flood and meteor shower.
  - Planets mode with N-body gravity and a slingshot challenge.
- **Build mode**
  - Toolbar with categories, a ghost preview, grid snap, rotation (Q/E) and move/rotate/scale gizmos.
  - Attach tool with 19 joint types, rope-over-pulley tool, and wiring for signals, power, water and air.
  - Paint, label, group, freeze, undo/redo, blueprints, a design library, and JSON save/load.
- **Parts**: about 170 parts and templates. Machines include:
  - pulleys and block-and-tackle, gears, belts, rack-and-pinion, worm gears, a differential, cams, an engine, a screw jack and a crank winch;
  - motors, servos, steppers, steering, pistons, thrusters, propellers and conveyors;
  - logic parts and a JavaScript controller block.
  - Vehicles: car, buggy with suspension, tank, boat, sailboat, submarine, hot-air balloon, helicopter, plane and glider.
  - Machine templates: catapult, trebuchet, crane, drawbridge, elevator, windmill, water wheel, pile driver, Rube Goldberg chain and a load-test bridge.
- **Tools**
  - Live inspector and debug overlays.
  - Ruler, protractor, stopwatch, and a graph window with CSV export.
  - Load test, replay with rewind and scrub, ghost runs, and side-by-side compare.
- **Modes**: Sandbox, Creative (zero-g, no breakage) and Challenge. Challenges are scored with 1 to 3 stars and include lifting with pulleys, bridging a gap, launching for distance, a hill climb, an earthquake-proof tower and a planetary slingshot.

Press **H** in the app for all keyboard shortcuts.

## Code layout

`index.html` is organised into commented sections (search for `// ====`):
`Core/Util · Materials · Textures · Audio · Renderer/Sky/Weather · Particles · Terrain · World · Kinds · Entities · Constraints (joints, motors, ropes, gears) · Behaviors · Systems (fluids, aero, fire, electricity, pneumatics, destruction, disasters, planets, soft bodies, ragdolls, arena) · PartsLibrary · Templates · SaveLoad · BuildTools · Cameras/Input · Inspector/Debug/Tools · Modes · UI · Main loop`.

Adding a part takes one registry entry:

```js
part('anvil', { name: 'Anvil', cat: 'Weights', icon: '⚒️', cost: 40,
  ent: { kind: 'compound', params: { parts: [/* boxes, cylinders… */] }, mat: 'metal', mass: 100 } });
```

The simulation runs on a fixed timestep and uses its own seeded random generator, kept separate from rendering. Visual effects use their own randomness and never feed back into the physics.
