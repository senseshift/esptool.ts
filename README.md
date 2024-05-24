# esptool.ts — TypeScript port of esptool

This implementation is used in the [SenseShift Web Flasher](https://docs.senseshift.io/docs/firmware/web-flasher). Forked and modified from [`toitware/esptool.js`](https://github.com/toitware/esptool.js).

<b>Get involved: [Discord](https://discord.gg/YUtRKAqty2) • [Website](https://senseshift.io) • [Issues](https://github.com/senseshift/senseshift-firmware/issues) • [Twitter](https://twitter.com/senseshiftio) • [Patreon](https://www.patreon.com/senseshift)</b>

[![Discord Widget](https://discord.com/api/guilds/966090258104062023/widget.png?style=banner2)](https://discord.gg/YUtRKAqty2)

[![Version](https://img.shields.io/npm/v/esptool.ts.svg)](https://www.npmjs.org/package/esptool.ts)
[![Downloads per month](https://img.shields.io/npm/dm/esptool.ts.svg)](https://www.npmjs.org/package/esptool.ts)

[![MIT](https://img.shields.io/github/license/senseshift/esptool.ts)](/LICENSE)
[![GitHub contributors](https://img.shields.io/github/contributors/senseshift/esptool.ts)](https://github.com/senseshift/esptool.ts/graphs/contributors)
[![GitHub](https://img.shields.io/github/stars/senseshift/esptool.ts.svg)](https://github.com/senseshift/esptool.ts)

## Example

The code is written as a library. Here is an example showcasing how you can flash an ESP32 using the library:

```typescript
export type Partition = {
  name: string;
  data: Uint8Array;
  offset: number;
};

// TODO: Here you have to specify the partitions you want to flash to the ESP32.
const partitions: Partition[] = [];

await port.open({ baudRate: 115200 });
try {
  const loader = new EspLoader(port, { debug: true, logger: console });
  options.logger.log("connecting...");
  await loader.connect();
  try {
    options.logger.log("connected");
    options.logger.log("writing device partitions");
    const chipName = await loader.chipName();
    const macAddr = await loader.macAddr();
    await loader.loadStub();
    await loader.setBaudRate(options.baudRate, 921600);

    if (options.erase) {
      options.logger.log("erasing device flash...");
      await loader.eraseFlash();
      options.logger.log("successfully erased device flash");
    }

    for (let i = 0; i < partitions.length; i++) {
      options.logger.log("\nWriting partition: " + partitions[i].name);
      await loader.flashData(partitions[i].data, partitions[i].offset, function (idx, cnt) {
        if (options.progressCallback) {
          options.progressCallback(partitions[i].name, idx, cnt);
        }
      });
      await sleep(100);
    }
    options.logger.log("successfully written device partitions");
    options.logger.log("flashing succeeded");
  } finally {
    await loader.disconnect();
  }
} finally {
  await port.close();
}
```

## Similar projects

* [Toit esptool.js](https://github.com/toitware/esptool.js): original library, this one was forked and modified from.
* [Espressif esptool-js](https://github.com/espressif/esptool-js): new project with similar purpose, but still not written as a proper library.
* [Adafruit Adafruit ESPTool](https://github.com/adafruit/Adafruit_WebSerial_ESPTool): the source of inspration for us, big shout-out thanks for spearheading the effort. The implementation is written as an application and hard to use as a library.
