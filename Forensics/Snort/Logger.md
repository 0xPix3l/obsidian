You can use Snort as a sniffer and log the sniffed packets via logger mode. You only need to use the packet logger mode parameters, and Snort does the rest to accomplish this.

Packet logger parameters are explained in the table below;

|               |                                                                                                                                                                                  |
| ------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Parameter** | **Description**                                                                                                                                                                  |
| -l            | Logger mode, target **log and alert** output directory. Default output folder is **/var/log/snort**<br><br>The default action is to dump as tcpdump format in **/var/log/snort** |
| **-K ASCII**  | Log packets in ASCII format.                                                                                                                                                     |
| -r            | Reading option, read the dumped logs in Snort.                                                                                                                                   |
| **-n**        | Specify the number of packets that will process/read. Snort will stop after reading the specified number of packets.                                                             |


```bash
sudo snort -dev -l .

# reading
sudo snort -r snort.log.1638459842
```