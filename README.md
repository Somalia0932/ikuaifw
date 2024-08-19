# ikuaifw
爱快固件收集 禁止违法用途
download with ipfs gateaway
like https://4everland.io/ipfs/QmV5ibLDRKow4wVrrps9RSjNEG8cyz33AEunLJ7kfNQJ7V <br>
format is https://4everland.io/ipfs/{cid}
beenwolk it if you like 
| cid | filename |
| -------- | ---- |
|QmS1RTyyLRR9Wh165hhKrGN4TgwjK5MkvJbainLSQ3uUqp|IK-MT7981V1-M5S_sysupgrade_3.7.5_Build202308311702.bin|
|QmV5ibLDRKow4wVrrps9RSjNEG8cyz33AEunLJ7kfNQJ7V|iKuai8_x64_3.6.3_Enterprise_Build202204081243.bin|
|QmViNmngDN815AehEYSVT9V8RXYtRk4fLVg9uvADdP2ej3|IK-MT7621_N7_5G_sysupgrade_2.0.1.bin|
|Qmb1KfDoVsTWEL9gk1zYWAJGN1Q2BJxcaTWVTvwC7gsFhy|IK-MT7621_N7_5G_sysupgrade_2.0.4.bin|
|QmQu5mT9LL8Mgthe15diXaX7gVc6wQFmecD3yWRuqACgZG|IK-WAQCA9531_W6S_5G_sysupgrade_1.7.1.bin|
|QmZpVUWeXHsLoi8Vs2xUPGf5NSDJ6tQH26wxyyCGCPYsvd|IK-WIPQ6018_X6_5G_sysupgrade_1.7.5.bin|
|QmQyU6SB2Sg1Wpc6Znq8FjU5AADASm4fzFx5sLHRwBWQe3|IK-WIPQ6018_X6_5G_sysupgrade_1.7.6.bin|
|QmafNuzENQJHFV2qJEcN5oGWPqiDEmfQngPxoFkdy9K8jg|iKuai8_x64_3.6.13_Enterprise_Build202301131535.bin|
|QmdrpzZTD4jCuC7iQMZw8GAeSJGCLrS11FMYdgS4ZAHFjL|iKuai8_x32_3.1.7_Enterprise_Build201903181458.bin|
|QmQDsW5bVhnyeF3VFsEo5yHqyynPfrZ23KnSypH25kifkX|iKuai8_x64_3.7.2_Enterprise_Build202304211809.bin|
|QmRhPgdsieD6J4ZCLacA2Tfr8CqufUzrcu5431JzdPGeRq|iKuai8_x64_3.7.5_Enterprise_Build202308081511.bin|
|QmQqJoyoR69TrQwCPdXp9cZTULSQXULZ1Rm1h4tWUrFy89|iKuai-GX2600_x64_3.5.4_Enterprise_Build202105071917.bin|

# rootfs获取
```
#!/bin/bash
srcfile=$1
UPDATE_DIR=.
headlen=$(printf "%u" 0x$(hexdump -v  -n 4 $srcfile -e '4/1 "%02x"'))
echo $headlen
printf "\x1f\x8b\x08\x00\x6f\x9b\x4b\x59\x02\x03" > $UPDATE_DIR/header.bin
dd if=$srcfile bs=1 skip=4 count=$headlen >> $UPDATE_DIR/header.bin 2>/dev/null
gunzip < $UPDATE_DIR/header.bin > $UPDATE_DIR/header_info.json
dd if=$srcfile bs=$((headlen+4)) skip=1 >> $UPDATE_DIR/firmware.bin 2>/dev/null
gzip -d < firmware.bin > firmware.bin.decompressed
mkdir mount
sudo mount firmware.bin.decompressed mount
cp mount/boot/rootfs ./
cp mount/boot/vmlinuz ./
echo '#include <stdint.h>
#include <stdio.h>
#include <stdlib.h>
#include <sys/stat.h>

uint8_t ext_key[1024];
uint8_t key[17] = { 0x77, 0xb1, 0xfa, 0x93, 0x74, 0x2c, 0xb3, 0x9d,
	0x33, 0x83, 0x55, 0x3e, 0x84, 0x8a, 0x52, 0x91, 0x00 };
uint8_t hash_table[] = {
	0x00, 0x00, 0x00, 0x00, 0x64, 0x10, 0xB7, 0x1D, 0xC8, 0x20,
	0x6E, 0x3B, 0xAC, 0x30, 0xD9, 0x26, 0x90, 0x41, 0xDC, 0x76,
	0xF4, 0x51, 0x6B, 0x6B, 0x58, 0x61, 0xB2, 0x4D, 0x3C, 0x71,
	0x05, 0x50, 0x20, 0x83, 0xB8, 0xED, 0x44, 0x93, 0x0F, 0xF0,
	0xE8, 0xA3, 0xD6, 0xD6, 0x8C, 0xB3, 0x61, 0xCB, 0xB0, 0xC2,
	0x64, 0x9B, 0xD4, 0xD2, 0xD3, 0x86, 0x78, 0xE2, 0x0A, 0xA0,
	0x1C, 0xF2, 0xBD, 0xBD
};

int32_t hash(uint8_t* a1, uint32_t a2)
{
	uint64_t v2 = 0;
	uint32_t result = 0xffffffff;
	uint8_t v4;
	uint32_t v5;
	while (a2 != (uint32_t)v2) {
		v4 = a1[v2++];
		v5 = ((uint32_t *)hash_table)[(v4 ^ (uint8_t)result) & 0xf] ^ ((uint32_t)result >> 4);
		result = ((uint32_t *)hash_table)[((uint8_t)v5 ^ (v4 >> 4)) & 0xf] ^ (v5 >> 4);
	}
	return result;
}

int main()
{

	int64_t key_index = 0;
	uint32_t initrd_real_size, tmp_var, hash_var;
	int8_t initrd_size_low_byte;
	int32_t var_0 = 0, var_1 = 1;
	uint8_t* initrd_start;
	int64_t initrd_size;
	struct stat* initrd_stat;
	FILE* f;
	uint8_t* p_hash;
	int64_t iter = 0;

	f = fopen("./rootfs.raw", "rb");
	initrd_stat = (struct stat*)malloc(sizeof(struct stat));
	fstat(fileno(f), initrd_stat);
	initrd_size = initrd_stat->st_size;
	initrd_start = (uint8_t*)malloc(initrd_size);
	fread(initrd_start, sizeof(uint8_t), initrd_size, f);
	fclose(f);
	// initrd_real_size = initrd_size - 4;
	initrd_real_size = initrd_size - 4;
	initrd_size_low_byte = (int8_t)(initrd_size - 4);

	do {
		ext_key[key_index] = key[key_index & 0xf] + 19916032 * ((int32_t)key_index + 1) / 131u;
		++key_index;
	} while (key_index != 1024);

	for (iter = 0;
	     initrd_real_size > (uint32_t)iter;
	     initrd_start[iter - 1] = ((initrd_start[iter - 1] - (uint8_t)tmp_var) << ((uint8_t)tmp_var % 7u + 1)) | ((int32_t)(uint8_t)(initrd_start[iter - 1] - tmp_var) >> (8 - ((uint8_t)tmp_var % 7u + 1)))) {
		tmp_var = var_0 + iter++;
		*(uint8_t*)&tmp_var = (uint8_t)(initrd_size_low_byte + ext_key[tmp_var % 1024u] * var_1);
	}
	printf("initrd_size:%ld\n"
	       "initrd_real_size:%d\n"
	       "iter:%ld\n",
	    initrd_size, initrd_real_size, iter);
	hash_var = __builtin_bswap32(~hash(initrd_start, initrd_size - 4));
	hash_var = __builtin_bswap32(~hash((uint8_t*)&hash_var, 4));
	p_hash = (uint8_t*)(initrd_start + initrd_size - 4);
	printf("calculated  hash_var:%x\n",hash_var);
	printf("authentic p_hash:%x\n", *(uint32_t*)p_hash);
	if (*(uint32_t*)p_hash == hash_var) {
		printf("success\n");
	}
	f = fopen("./rootfs.decode", "wb");
	fwrite(initrd_start, sizeof(uint8_t), initrd_size, f);
	fclose(f);
	free(initrd_start);
	free(initrd_stat);
	return 0;
}' >dec.c
gcc -o dec dec.c
cp rootfs rootfs.raw
./dec
mv rootfs.decode rootfs.xz
umount mount
rm -r mount
```
之后解开rootfs.xz，里面是个一个ext或者其他格式的文件系统镜像
![image](https://github.com/2512500960/ikuaifw/assets/16852896/38758266-13e1-4fca-b157-c101f2023950)

![image](https://github.com/2512500960/ikuaifw/assets/16852896/d60bc06e-e376-481a-bdfe-a628318d7284)

# ikuai-包管理器-pmd
pmd里面有三个地方用了加解密，更新消息、数据库文件、pkg包
加解密除了算法还需要密码和saly用来生成aes-256-cbc的秘钥和iv，
pkg包的密码来源应该是在线获得（安装）或者从数据库文件中查询（安装后重启）
pkg包的salt来源在包的前16个字节中的后一半
![image](https://github.com/2512500960/ikuaifw/assets/16852896/8cec03db-b7ff-4807-9cae-46e25ec7abba)
解密后那个文件开头是一个sh脚本，后面是一个gz压缩包
![image](https://github.com/2512500960/ikuaifw/assets/16852896/bdb84ad9-e402-4e29-a339-94d2bbc49b62)
解密后的pkg文件头部的shell脚本本身就是个安装程序，打包好一个包含了应有文件的软件包，然后加上个shell头，然后加密，加密后再把软件包的加密密码放到数据库文件里面
![image](https://github.com/2512500960/ikuaifw/assets/16852896/ca21072e-4584-4a39-98fb-a46dbad04e25)
# 解密pmd数据文件/etc/log/packages/db/.__DB.3.x86_64
```
ciphertext='''
w3YVBhGrgL+QVE2iIL7xGOh0W5ihhQBgKDeBbvGqJVzlIrNFB3RIlzpNxYCn/MDfAo62Q6bw
OcuewqlqaryIr9cfB8dhj4H2BPSDEPugBbYTBYAW7sqcYudIsHH1GfT9H3e9R1xdfGCv45m7
qnIRtgTAuin+5JOz3a1cnRgr0+xS31cDRlcidi4hyTC2og5t846RtBeuWBI6bvBgztQboF7x
s4vMLzV/l2TVPwL46c5rpLzKI3YXXVP+lKJsQigCEgKVReDNfTlMPRqfb6kont17z4m7JhMF
RFznMb+NiLMTXCfh0AujvekDtwM9z6uhIgXIR+256WaVQTuBEPg8CkSAKwWGnSdS0aGAN5tH
/CxSirVoNaFByMi0nJJ8lTOVL3kiz6dUA9Xid4w5RHt+D/h7XXS5kbQB4g+3914+ZhnuVPCL
M46bNTftM4IJykde7E9VXo6mIaKsmYDMgNh/+3ZznRl2+xBpxAeR/9AACzurt1SN+sTwS4s7
0NaECuO5UVa4RuTdLQc1PUqSgqx2OYbDZkFtg8D5Y4MRulpScn5+mvwVcCLCD/mlrHNHFzeu
1acrmWo3H1876DXwLdfk440611myKUVUSFLbwjd2v++Cl3PTY/xl+Qg9Od8uxRV3W01WFucV
AKcKgJETIcgkkt8vlbCWWsK9xOec3JzZDmraqLMobhhe1C7Skh/lKI1cnt68WY03tZn6JJ7S
D+NKxGKBpyvAwIXqbfia67Mf1nLoZ2T1NvSAZ6b9/ars5BNX1hac8cwfTP7y76bXZgPxZAtY
r6ZO+aOe95A8xVZtKZeQESrm1ToYzX/6yMvmK46yg0HXzovzW+VPdJTxtlXyTzdv4qP4jaDD
2YtoXIEcdjZJd2Y6QR0pYNjTuu9OO2POCNcrBxIEO2uS+210laWVIjZNCwMDX0Vqd2pIVQqY
bA+gB4QgYed8tdLEK79ieNWl8lNU9KAdnHgsVrO1YwcBVjY+N9aw7iqrZJZSH5qt50Um+o4V
PWsWYTgooswYRgk19v+SOgn5FAfigNcbrlGbZC3RORE/U3uG1307Dif3I2wTB9UvpcW0G4NY
t8SB5dO4ZDgCFdr/1cyEC5NvZvZeDFxmGYhoNq8WOyQDrYMs0IE0XyuI+4fQ6fyGJz0nawzo
pInCeFiWMy1qwau0V7KTVtChjubhxIvkjOP5BHDTyIYxnqsUfgmOaSVJlo1fKpxPMeQOZYlr
/oUJBDFXiFvxTCNhZuQwQVNEK9o7woPY6qxGWlch5MDkjnTGotFWNstn5az54T1btka+wXlj
ogLEyqD4CxMPsKJh32O6RQbCmhhnMsXQDM5VZ6ioipd7y2Wc2uRJM081WE+kB8C60RpkbBHd
429Kdv5oKlGMH5VEQzriQvX3P0JxOxMEX5bYphAV+g7z2DXFilSB8r2zCy97TAKkPaHzMDOY
op3gYQlGO/wewzy4c/hf3TOjgvEMV7OWJyQnUhGkZoIUifanD0SkKmRp2D85BWAYWCFeGNUu
mCk+tzk9nmCOBtF/9H1NwBbPHnphKJSQYDYanXwlrNXbhMs0t34cj/QJDwFiqYBhpImn/XgP
DJ8LTFXsh06lO1hG5O+nNlk4KAdSNvrTqpcPY7IhLwbhMacxBRkzHqvaWKVpc5VNpU+QRlEH
2HuO4VOfkKLB9tUhHX4/yVA/BsI4aP885Dq8bnHJWL8+7iVrzFDzkPBGeM2kPw7xpemwp9Zz
QLNQ1u/PJTs2AxbziiAnTiMJgA8omDZF70pOsdAPDYp8ogiynl7GDt+7agfESMjDTCUrn7wq
lH5figyNea1jUrO3joB7XOCZiIfmh//6KQjC4XcY164l0bL1FqB5DpiM+yqyDmGDtDxEUIoM
WJ9ko7zcH7jdjcUCAMYbpsP2sAr7J25a1eZURH9gPACO3jDmk2zlvDiL1j9EUX6GtwnrcG3b
3+WcYc8tRviWKFPmiL7fju706Iwm/ekbOcF3HKXxXEZNNPFDKmWs68itFNEdXSxmcgdnv9tR
o4f7UJzVToU1a904xp4YdawdnbzpgiHLAZtyJ4U2Q+ihfKazrYwQD0Ys8wK20SQZDosxbWQe
tZpKVZYoieEJ8V39q/gLG0GfEgT19bir+kKzl+UnAQRppwwj6QGx2kz8qDYYoKbX4TsvPfqP
J40gQXxN1BUFGOmRdp6Vlwu1JvFipUD54/iA1TVp/19o28uzWJkekFs9R3dhqLeMEy6ppECT
DpKAYmddEna9n7CPiPpBAwhW1QblPeiNFtyzn1fqgf0qGfEhC4RllSH1g5Vb20P+6D9uzarg
zxne9cJ1gv2LSIMg7E4ph+mtcocq4AiIpq8wyTiiA1BGZSbU9O1DO2paKfKHc1ZMnKTxJ2+J
6oBPQ1eXA/JjO/LoJg7kka9UwteHLunHlnFwZJOpibu91R56OotOvaDhTrr50aFQzu/PMvsf
jW+fiTX84D4Hg0EBnJKO3VN2CnYlNs9AY0ANNwQ2lmfKqveCf/CXO7NgD+C1eVVRhBFHxYLo
oex6APrTimaIkU2ad6RysRnbExLTOnmAtLPf2jjaGRoMNYudK1FH+gWX1Qgvkk9B1Z6q+DMb
KtWgqczM/vu83spLqyuGJekfS8QEU9IMJ+jEOQShLRWzZ1L7MeZax3uynwRhbH8O9k2mk56h
N/SWTQjjuaKHan3lJgXLvj0AEMwpi0R9rPE9sO8gUHi+ZrZ87uV6XrJYdA3QUlp7XE3/Kdug
ldZmpa/Qc6WifDGLRsLZ/f+UVLMLfVW27aUdPO2vRwZfCJnJnDAmZqBYbOrJz0emZ+t6T+HM
9fciRIMc1aaRrYMnEjGzaRrvphksLViAvjRjIyLTPeZGajiAKqYZcRiobSMvjD0/lTpL8Ikx
DTZL5mi4xCtkfvtIGzNISO6toJVeBNZ4CX5iCJ1GV9IhMbVNFh8q4T3icVos84W7VrDwMFUr
/NZd2g4vfF0IrmDXuKWER/43ybSRJOc0vXG5E+3Dfn9XL7HO3KwizCGUASMFUd/SfkyPb5JO
FThS6ur8bQGqB8xtCBss6mnrTWl/3gfSr6Te/tP1U+rPz1i1wg7KsUwqiiFQM61RHxtT2FpG
pFXn5fdqgFsYMiILY4D9vjCU4k/uuKmtRhucUPK1izL4rNbzYkFa+LLXrWKI1jFWyYVoF+8G
q6kPK474jNqkDSlvkJoNlj51SCCbb/9uFddTJOezfflaAeb+bY1/KFdd5T70g09nSBdBONHS
LzICUWrbOUwSgb7XGIDk+Q7EiDChQbgQepbuSzXh8qNQuFWc4HFkQGuIfhtgM+8JwJLNiOLi
95CMQ+ggmJrIKwizetI/iVgBQ7BxarOIWjpgmwMW0WfuZZOpmiZtyV1MC6I9ZtaM+kjuBxA4
Bydp/EfXhfhtoMU1ybZ7y6bYN2vH9LThisBLSnYVUlkFMH+O56hyQ3YCFAcm+6rzcVgZbudm
NWPSIP7FQsI0EsUAGhCLMZNk5cmkv2edFkBLl1nnwlpNKAFS1bSwiO4AMs/S8zDhLp6TdUUB
XhSyQozIfDQb+Tr2eptf/RnkXpp6PsDdbkupf12WmyLkwa5ZFbB/FRRXC22tygmgxNru/1lt
p6xmkDHkCisg+gKmKtGzAss8lRqBIH67WS+Ni62brs/XdCXP7wH8HickjLK6LK9AncfJiqbc
n1xqwbOqMX+jhtClISfAUSGUejL/kC85fK4VzH8B5m+MY3K97wJVslRNnnrvilH3FiGFttko
iLmnRP0fAbzF7VWkuHyJZAEFxxfYK7WWNY3PVl+povF7PvPb4nIgYgf5Rm/HYzfpa2/CB2Ni
q48XiCm3aD0ogVqYEV/E6vGclIxXCqm3c+dkfyAkfI+mCpqkWZXa4OCxPBlwODq/rDlHG9Ws
ncaJiE9cF0LuqajvpsGAlstiUWsDq8tbIVLn/sZ9EZ3NS20X9KXRqFG/s4aC9+98afeo7TN0
FlfV5rALalLLExaPlgASfOpnDbP1MQGQXfRQv1Afb2G2rqQKuymzSgTqNNVB+edkRqmLwU0x
/NFkKy18jT6OYl4nJSr9iwCkePwKvNvObv0yXN3QVruDMpeS7iTdQLGQB1a9nwsWtF10y2TM
6ZvVbPR73Vkyywpw3rA3j6RTbiD7D350cgxU5X8pkQH1xbILEZH/hTL6j0YKloCmf55dWoq1
RjTYHK77RjZkW4vvnmZD79EicBYCTmeMTZx8STR8D7fzYCBc6cVmmKO/ntTtF49rHMiW79r5
Nx6baal463VDlNbnsYZdtk8Un1r2HNKm3O2+3hs/IfnLAIEv5HhcZZ/4nKRE+euoe1MqEBFS
Qg6gE3HgTRjGH6T6/WVu1owWXCYsRQU8h3GhbgK3R5INemqvQKLSAYGmyuvf9+cRIiYP+Vyk
lcog9ocOCHSWsx+8/xC1IdZJ6ZlD1ZBr1terM8gdJgugm4J9NPor6rOl/Fl/9S6VpxMeX/LJ
Yfh4Kae2PksVafwVPfScyKqpKhawq1byEFl2M8kn3kF3S2db8yAcTN1j4XxPb8H34syfWHIS
L4xxDzKyijgQJ43yDbgdGry/P6Tf7T3JuAt1Nr4M+P81fO19ZgOU93ouVgLWXmCLd1friqZw
sDuOflil1LwKxv0uflQm6BzFloQBtBQu6j74ZeC4dmqkPBA0/6KMrMrxWQ25wbsoNM9V2pys
AcUdw9ZAl7A66Yuu4bRCbkv4nj4Avsv6ywJdUiX59a5dY9zEyJbAnV3QGi3QxF8F8i6SjlNc
cOq5Mxk6k4OUPV5slX3zRE3MGUKAaQ98vWh7j/Hl/kJBcJT4TS31igWmumEU6UKm8AG/WkbR
A45uVx1nidqc4wzn3Bs1bV6yOzhbW1Z9fkQNQSsSP7xVJ/c7sy4keW6pOTBJ/LswIqG2PtLE
0LM6v9CbulH09q4ad1lAeJtfjDytRrG6PtXcZmxXqVUKloKAmdCYxnCLhMwl8MX3fxzpLvWz
kS11j2YmtX1ZzarroPgyuTsPsp1wviaUJEqmhP/6P6bY6+ZwIMgbZvC55d2MYAPzS1eqJLRh
HAR8LcxyDR2Zu18bFgabxCJMxNW7Gk5lh179H30v036nlVjdyospht3vTktipHYuWPUhE386
hRKRzJ3WD2kk/znGV9Eh0lUE8bkJlQBkuK1RdxHlEEZgIu3BbYYE0e0o6KP6xTCMBqYjV7pM
ZfUNGbTTqUH7ln/1sXyvO/SXsT5v9TfSEvkzmPPgtyKoT3pQdfxFW09vP96qCyC3pcMwtEP9
97f4DM+cA66liZB7Pebc8TCmlpneSnggohoP6+j4wV/zXzqBDJ1s3fZ+pM/q6JIpP/CpyNPW
7an8qgTK4AkhVg6+Jd7lckU4yab4/dRVK0xuCFFm1ZNAYuB6oA787/fX0r3s8mQdLc5vU1DA
qvkMebSB4iDiE+e3kIGUc1Voo1JxN9lvMIPSYlxou8nDK2jyR7lVtc3h2rETcsd7k3CDCF+k
VJUEaXUWDxYLEcHqkEibWIEBCHV9SeiQ+G3epskLPncA/iXOZ2aYmwq25EHuia17gQzaslG7
vz1eaxq9s0iiq8+O8bjnCpedDVgOipxtTgnGYw0nEjWMl7m1ums163ODes8y4nhdN8r0yDct
O/dvyZTliYHV4e624uGyfcaefkHCij+xyzs7pG4qtzIStp8M3Yp3KXarsoNgnvv0eRlc0iiF
D/GYMvEVyHjVISwjvpgYBOIlyKZHDcVP1WtEIdgyL3+PxVWZVhntJ2ct010Agjpkree59VOa
m/KmOv3uCZ22XjuPGswH6E/pr9S/Pr0rETlSRuSeIYFrWYk4GXVKCX/T6L8J9qxgWp1xyazd
5lsSHIU71sVBSajpBrDYOYAoJxPHQ2GAvjRL94FERd8WUUku2yQa2iSWcx2UpAIK0zh0o70d
gn4t4Yz7z9YCY4Al677p+j/KcyKYQvHp8FJfKk70OagnWsl5Omr2EbhESwGNr5qEC2Ww3A3K
FsbEPCWkoOJn+3j1wLKjWcOuIpRb3DzSnTYkoV+UU/XP80ZJImIvlxhgsnRxtMVYq7m+nNCb
q4AWUOtZr3b9+utWiuOujzxuKp1tGo7sDqTbxlYNSUOhZo4HvZCbqUF6xLlL0mafeVJ7GiM3
wRBU5ILsa1BOo32Kmz0wNGPKwl9+dL5lu5Op20rEsG/JYvF/Fz7LY5gq1CwrwyB2rK5yCgdT
+dZ45guRDwTVOwOGzYyBKQiLQvciM+sewIMV4Z98+HN9vV+Ka1m3tFpgMDADca0FYkSNWDRW
2d/qXfFzEk50GSKKTk9mY1vgtAkB5ScGoVoYVQZ60JahkQLA1yti3+6YwH8ukX35fW7G+vQT
L4DlpEg0VkHiSOxX07UNYsSYI227belUcX9YT1oQyjNeOZa3tXMADrnB4/f+IdVvBP97jiHW
erJ5oSn31FkP5DPtCPkS7TGkPWG9PmM3S1zFnEXVb+G3Pdo1Eq2PORQE7Cc9eepfZjWNX363
zuClVeyTILioBtU/KIZhLGK4z2imJsjoeP6Hjc+d3qjREFAUEHDFN+qq0mxpHEg5imorHG8d
dxOs2nllIjTQv2NO+n8QY+SDg5o/iBI4HKON7BEq4w0IiLSYvG8bYub/50lSu7vu9xB2LO82
5f47dnCm2TchdV3NVxfMwEZvJ3rW44IxWa35tikxR6ZFALrH0endMSpbCgBa7vLdug1IKkyh
Zkyme22EPWaAKnpJlCc+8hpy/p/whxqus/7XzuNSQUVi4g==
'''


from Crypto.Cipher import AES
from Crypto.Protocol.KDF import PBKDF2
from Crypto.Random import get_random_bytes
import base64
import hashlib, binascii
from passlib.utils.pbkdf2 import pbkdf1

def hasher(algo, data):
    hashes = {'md5': hashlib.md5, 'sha256': hashlib.sha256,
    'sha512': hashlib.sha512}
    h = hashes[algo]()
    h.update(data)

    return h.digest()

# pwd and salt must be bytes objects
def openssl_kdf(algo, pwd, salt, key_size, iv_size):
    if algo == 'md5':
        temp = pbkdf1(pwd, salt, 1, 16, 'md5')
    else:
        temp = b''

    fd = temp    
    while len(fd) < key_size + iv_size:
        temp = hasher(algo, temp + pwd + salt)
        fd += temp

    key = fd[0:key_size]
    iv = fd[key_size:key_size+iv_size]

    print('salt=' + binascii.hexlify(salt).decode('ascii').upper())
    print('key=' + binascii.hexlify(key).decode('ascii').upper())
    print('iv=' + binascii.hexlify(iv).decode('ascii').upper())

    return key, iv

def aes_decrypt(password, ciphertext, salted=False):

    if salted:
        # skip header "Salted__"
        ciphertext = ciphertext[8:]
        
        salt = ciphertext[:8]
    else:
        salt = b''

    key_length = 32  # AES-256
    iv_length = 16
    key=None
    iv=None
    key,iv=openssl_kdf(algo="md5",pwd=password,salt=b'',key_size=key_length,iv_size=iv_length)

    cipher = AES.new(key, AES.MODE_CBC, iv)
    plaintext = cipher.decrypt(ciphertext)

    return plaintext.rstrip(b'\0')


password = b'ikupdat-d~#-'
encrypted_data = base64.b64decode(ciphertext)
decrypted_data = aes_decrypt(password, encrypted_data, salted=False)
print(decrypted_data.decode('utf-8'))
```
![image](https://github.com/2512500960/ikuaifw/assets/16852896/3d23f25b-9a22-4a87-809d-bc567c01c435)
# 解密单个pkg包，密码在db文件中记录
```
from Crypto.Cipher import AES
from Crypto.Protocol.KDF import PBKDF2
from Crypto.Random import get_random_bytes
import base64
import hashlib, binascii
from passlib.utils.pbkdf2 import pbkdf1

def hasher(algo, data):
    hashes = {'md5': hashlib.md5, 'sha256': hashlib.sha256,
    'sha512': hashlib.sha512}
    h = hashes[algo]()
    h.update(data)

    return h.digest()

# pwd and salt must be bytes objects
def openssl_kdf(algo, pwd, salt, key_size, iv_size):
    if algo == 'md5':
        temp = pbkdf1(pwd, salt, 1, 16, 'md5')
    else:
        temp = b''

    fd = temp    
    while len(fd) < key_size + iv_size:
        temp = hasher(algo, temp + pwd + salt)
        fd += temp

    key = fd[0:key_size]
    iv = fd[key_size:key_size+iv_size]

    print('salt=' + binascii.hexlify(salt).decode('ascii').upper())
    print('key=' + binascii.hexlify(key).decode('ascii').upper())
    print('iv=' + binascii.hexlify(iv).decode('ascii').upper())

    return key, iv

def aes_decrypt(password, ciphertext, salted=False):

    if salted:
        # skip header "Salted__"
        ciphertext = ciphertext[8:]
        
        salt = ciphertext[:8]
    else:
        salt = b''
    # skip salt
    ciphertext=ciphertext[8:]
    key_length = 16  # AES-128 16    aes-256 32
    iv_length = 16
    key=None
    iv=None
    key,iv=openssl_kdf(algo="md5",pwd=password,salt=salt,key_size=key_length,iv_size=iv_length)

    cipher = AES.new(key, AES.MODE_CBC, iv)
    plaintext = cipher.decrypt(ciphertext)

    return plaintext.rstrip(b'\0')


password = b'599eba19aa93a929cb8589f148b8a6c4'
encrypted_data = open("netboard.bin.pkg","rb").read()
decrypted_data = aes_decrypt(password, encrypted_data, salted=True)
open("netboard.bin.pkg.decrypted","wb").write(decrypted_data)
```

