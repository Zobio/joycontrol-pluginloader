# joycontrol-pluginloader

[English](./README.md) / [日本語](./README_ja.md)

This is a plugin loader for joycontrol that can emulate Nintendo Switch controller over Bluetooth.  
[GitHub - mart1nro/joycontrol](https://github.com/mart1nro/joycontrol)

## Installation

- Install joycontrol  
  Details: [GitHub - mart1nro/joycontrol - README.md](https://github.com/mart1nro/joycontrol/blob/master/README.md)

    ```sh
    $ sudo apt install python3-dbus libhidapi-hidraw0
    $ git clone https://github.com/mart1nro/joycontrol.git
    $ sudo pip3 install joycontrol/
    ```

- Install joycontrol-pluginloader

    ```sh
    $ git clone --recursive https://github.com/Almtr/joycontrol-pluginloader.git
    $ sudo pip3 install joycontrol-pluginloader/
    ```

## Pairing the Pro Controller of joycontrol

1. Open the "Change Grip/Order" menu of the Nintendo Switch

    Home > Controllers > Change Grip/Order

1. Run the script

    ```sh
    $ sudo joycontrol-pluginloader plugins/tests/PairingController.py
    ```

    Run results:

    ```sh
    $ sudo joycontrol-pluginloader plugins/tests/PairingController.py
    [17:03:13] joycontrol.server create_hid_server::58 WARNING - [Errno 98] Address already in use
    [17:03:13] joycontrol.server create_hid_server::60 WARNING - Fallback: Restarting bluetooth due to incompatibilities with the bluez "input" plugin. Disable the plugin to avoid issues. See https://github.com/mart1nro/joycontrol/issues/8.
    [17:03:13] joycontrol.server create_hid_server::65 INFO - Restarting bluetooth service...
    [17:03:14] joycontrol.device set_name::69 INFO - setting device name to Pro Controller...
    [17:03:14] joycontrol.device set_class::61 INFO - setting device class to 0x002508...
    [17:03:14] joycontrol.server create_hid_server::84 INFO - Advertising the Bluetooth SDP record...
    [17:03:14] joycontrol.server create_hid_server::94 INFO - Waiting for Switch to connect... Please open the "Change Grip/Order" menu.
    [17:03:16] joycontrol.server create_hid_server::98 INFO - Accepted connection at psm 17 from ('01:23:45:67:89:AB', 17)
    [17:03:16] joycontrol.server create_hid_server::100 INFO - Accepted connection at psm 19 from ('01:23:45:67:89:AB', 19)
    [17:03:18] root _reply_to_sub_command::295 INFO - received output report - Sub command SubCommand.REQUEST_DEVICE_INFO
    [17:03:18] root _reply_to_sub_command::295 INFO - received output report - Sub command SubCommand.SET_SHIPMENT_STATE
    [17:03:18] root _reply_to_sub_command::295 INFO - received output report - Sub command SubCommand.SPI_FLASH_READ
    [17:03:18] root _reply_to_sub_command::295 INFO - received output report - Sub command SubCommand.SPI_FLASH_READ
    [17:03:18] root _reply_to_sub_command::295 INFO - received output report - Sub command SubCommand.SET_INPUT_REPORT_MODE

    <snip>

    [17:04:04] root _reply_to_sub_command::295 INFO - received output report - Sub command SubCommand.SET_NFC_IR_MCU_CONFIG
    [17:04:04] root _reply_to_sub_command::295 INFO - received output report - Sub command SubCommand.SET_PLAYER_LIGHTS
    [17:04:04] JoycontrolPlugin.loader load_plugin::9 INFO - Loading: plugins/tests/PairingController.py
    [17:04:05] plugins/tests/PairingController.py run::11 INFO - Pairing completed.
    ```

    > note:  
    > '01:23:45:67:89:AB' is your Nintendo Switch Bluetooth Mac address.
    > This Mac address varies depending on the environment.
    > You will use this Mac address later.

## Usage

- Basic Usage

    ```
    $ sudo joycontrol-pluginloader -r 01:23:45:67:89:AB <Joycontrol Plugin path>
    ```

- Options

    ```
    usage: joycontrol-pluginloader [-h] [-p [PLUGIN_OPTIONS [PLUGIN_OPTIONS ...]]]
                                   [-d DEVICE_ID] [-r RECONNECT_BT_ADDR] [-v]
                                   plugin

    positional arguments:
      plugin                joycontrol plugin path

    optional arguments:
      -h, --help            show this help message and exit
      -p [PLUGIN_OPTIONS [PLUGIN_OPTIONS ...]], --plugin-options [PLUGIN_OPTIONS [PLUGIN_OPTIONS ...]]
                            joycontrol plugin options
      -d DEVICE_ID, --device_id DEVICE_ID
      -r RECONNECT_BT_ADDR, --reconnect_bt_addr RECONNECT_BT_ADDR
                            The Switch console Bluetooth address, for reconnecting
                            as an already paired controller
      -v, --verbose
    ```

## Plugins

- [Almtr/joycontrol-plugins](https://github.com/Almtr/joycontrol-plugins)

  Demo:  
  [![](https://img.youtube.com/vi/JlspXe20-Dc/0.jpg)](https://www.youtube.com/watch?v=JlspXe20-Dc)  
  https://www.youtube.com/watch?v=JlspXe20-Dc

## How to create a plugin

- Create a file (e.g. ``SamplePlugin.py``)

    ```python
    import logging
    from JoycontrolPlugin import JoycontrolPlugin

    logger = logging.getLogger(__name__)

    class SamplePlugin(JoycontrolPlugin):
        async def run(self):
            logger.info('This is sample joycontrol plugin!')

            logger.info(f'Plugin Options: {self.options}')

            logger.info('Push the A Button')
            await self.button_push('a')
            await self.wait(0.3)

            logger.info('Tilt the left stick down')
            await self.left_stick('down')
            await self.wait(0.3)
    ```

- Load and run ``SamplePlugin.py``

    ```sh
    $ sudo joycontrol-pluginloader -r 01:23:45:67:89:AB plugins/samples/SamplePlugin.py --plugin-options option1 option2

    <snip>

    [20:12:44] JoycontrolPlugin.loader __load_plugin::22 INFO - Loading: plugins/samples/SamplePlugin.py
    [20:12:44] plugins/samples/SamplePlugin.py run::8 INFO - This is sample joycontrol plugin!
    [20:12:44] plugins/samples/SamplePlugin.py run::10 INFO - Plugin Options: ['option1', 'option2']
    [20:12:44] plugins/samples/SamplePlugin.py run::12 INFO - Push the A Button
    [20:12:45] plugins/samples/SamplePlugin.py run::16 INFO - Tilt the left stick down
    [20:12:45] JoycontrolPlugin.loader start::55 INFO - Stopping communication...
    ```

## References

- [GitHub - mart1nro/joycontrol](https://github.com/mart1nro/joycontrol)
- [Discord - Joy-Con Droid](https://discord.com/invite/SQNEx9v)
- [GitHub Gist - colemickens/amiibo-emulation-with-linux-vm.md](https://gist.github.com/colemickens/b08d1a339f4483c6b3c08e739d6cf5d0)