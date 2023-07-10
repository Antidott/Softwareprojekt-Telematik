# Minicom Setup

## Find correct serial port
Identify port via the `dmesg | grep tty`command

## Minicom Settings

1. install minicom: `sudo apt-get install minicom`
2. launch minicom in configuration mode: `minicom -s`
3. enter settings for serial connection
4. set serial device: `/dev/ttyXXX`
5. set Bps/Par/Bits: `115200 8N1`
6. set Hardware Flow Control: No
7. set Software Flow Control: Yes
8. exit serial settings
9. save setup as dfl

## Send binary to board

1. start minicom and connect with board: `minicom`
2. prepare board for update: send command `update_bin`
3. open minicom file transfer menue: `ctrl-A S`
  3.1. select 'ascii' as transfer mode
  3.2. select file
