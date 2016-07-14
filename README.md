# Ble//分包发送蓝牙数据
-(void)sendMsgWithSubPackage:(NSData*)msgData 
                  Peripheral:(CBPeripheral*)peripheral
              Characteristic:(CBCharacteristic*)character
{
    for (int i = 0; i < [msgData length]; i += BLE_SEND_MAX_LEN) {
        // 预加 最大包长度，如果依然小于总数据长度，可以取最大包数据大小
        if ((i + BLE_SEND_MAX_LEN) < [msgData length]) {
            NSString *rangeStr = [NSString stringWithFormat:@"%i,%i", i, BLE_SEND_MAX_LEN];
            NSData *subData = [msgData subdataWithRange:NSRangeFromString(rangeStr)];
            NSLog(@"%@",subData);
            [self writeCharacteristic:peripheral
                       characteristic:character
                                value:subData];
            //根据接收模块的处理能力做相应延时
            usleep(20 * 1000);
        }
        else {
            NSString *rangeStr = [NSString stringWithFormat:@"%i,%i", i, (int)([msgData length] - i)];
            NSData *subData = [msgData subdataWithRange:NSRangeFromString(rangeStr)];
            [self writeCharacteristic:peripheral
                       characteristic:character
                                value:subData];
            usleep(20 * 1000);
        }
    }
}
