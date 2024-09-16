<template>
  <div>
    <h1>Check the console log...</h1>
  </div>
</template>

<script lang="ts" setup>

import { BitByBitOCCT, OccStateEnum } from '@bitbybit-dev/occt-worker';



onMounted(async () => {
  let bitbybit = new BitByBitOCCT();
  const occt = new Worker(new URL('./occ.worker', import.meta.url), { name: 'OCC', type: 'module' })
  await bitbybit.init(occt);
  await bitbybit.occt.occWorkerManager.cleanAllCache()

  bitbybit.occtWorkerManager.occWorkerState$.subscribe(async s => {
    if (s.state === OccStateEnum.initialised) {
      console.log(`Initialised`)
      await stressTest(bitbybit);
      // renderer.setAnimationLoop(animation);
      // setShowSpinner(false);
    } else if (s.state === OccStateEnum.computing) {
      console.log(`Computing`)
    } else if (s.state === OccStateEnum.loaded) {
      console.log(`Loaded`)
    }
  });

  const stressTest = async (bitbybit: BitByBitOCCT) => {
    if (!bitbybit)
      return
    const wire1 = await bitbybit.occt.shapes.wire.createCircleWire({
      radius: 1000,
      center: [0, 0, 0],
      direction: [0, 1, 0]
    });

    const vect = await bitbybit.occt.shapes.wire.derivativesOnWireAtParam({
      param: 0.0,
      shape: wire1
    })
    console.log(vect[0])
  }


})
</script>
