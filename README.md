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
		v5 = hash_table[(v4 ^ (uint8_t)result) & 0xf] ^ ((uint32_t)result >> 4);
		result = hash_table[((uint8_t)v5 ^ (v4 >> 4)) & 0xf] ^ (v5 >> 4);
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

