# freetype
为什么用，对矢量字体的搜索
开源库的使用
FontsInit()->FreeTypeInit()->FreeTypeFontInit()
原理：freetype是字体搜索引擎，将文字编码和字库文件的字形一一对应。
可对字体缩放和旋转。
实现步骤：
1初始化库
2创建face object（字形和库的对应关系）
3可加载字体缩放和旋转
4通过编码值加载对应的字符点阵
5最后通过把字符发写入lcd映射地址上
```c
static int FreeTypeFontInit(char *pcFontFile, unsigned int dwFontSize)
{
	int iError;

	/* 显示矢量字体 */
	iError = FT_Init_FreeType(&g_tLibrary );			   /* initialize library */
	/* error handling omitted */
	if (iError)
	{
		DBG_PRINTF("FT_Init_FreeType failed\n");
		return -1;
	}
	
	iError = FT_New_Face(g_tLibrary, pcFontFile, 0, &g_tFace); /* create face object */
	/* error handling omitted */
	if (iError)
	{
		DBG_PRINTF("FT_Init_FreeType failed\n");		
		return -1;
	}
	
	g_tSlot = g_tFace->glyph;

	iError = FT_Set_Pixel_Sizes(g_tFace, dwFontSize, 0);
	if (iError)
	{
		DBG_PRINTF("FT_Set_Pixel_Sizes failed : %d\n", dwFontSize);
		return -1;
	}
	return 0;

```

留个疑问？如何写入lcd控制器映射

# libjpeg
// 分配和初始化一个decompression结构体
// 用jpeg_read_header获得jpg信息
// 设置解压参数,比如放大、缩小
// 启动解压：jpeg_start_decompress	
// 一行的数据长度
// 循环调用jpeg_read_scanlines来一行一行地获得解压的数据，并写入lcd上

```c
static int GetPixelDatasFrmJPG(PT_FileMap ptFileMap, PT_PixelDatas ptPixelDatas)
{
	struct jpeg_decompress_struct tDInfo;
	//struct jpeg_error_mgr tJErr;
    int iRet;
    int iRowStride;
    unsigned char *aucLineBuffer = NULL;
    unsigned char *pucDest;
	T_MyErrorMgr tJerr;

    fseek(ptFileMap->tFp, 0, SEEK_SET);

	// 分配和初始化一个decompression结构体
	//tDInfo.err = jpeg_std_error(&tJErr);

	tDInfo.err               = jpeg_std_error(&tJerr.pub);
	tJerr.pub.error_exit     = MyErrorExit;

	if(setjmp(tJerr.setjmp_buffer))
	{
		/* 如果程序能运行到这里, 表示JPEG解码出错 */
        jpeg_destroy_decompress(&tDInfo);
        if (aucLineBuffer)
        {
            free(aucLineBuffer);
        }
        if (ptPixelDatas->aucPixelDatas)
        {
            free(ptPixelDatas->aucPixelDatas);
        }
		return -1;
	}

	jpeg_create_decompress(&tDInfo);

	// 用jpeg_read_header获得jpg信息
	jpeg_stdio_src(&tDInfo, ptFileMap->tFp);

    iRet = jpeg_read_header(&tDInfo, TRUE);

	// 设置解压参数,比如放大、缩小
    tDInfo.scale_num = tDInfo.scale_denom = 1;
    
	// 启动解压：jpeg_start_decompress	
	jpeg_start_decompress(&tDInfo);
    
	// 一行的数据长度
	iRowStride = tDInfo.output_width * tDInfo.output_components;
	aucLineBuffer = malloc(iRowStride);

    if (NULL == aucLineBuffer)
    {
        return -1;
    }

	ptPixelDatas->iWidth  = tDInfo.output_width;
	ptPixelDatas->iHeight = tDInfo.output_height;
	//ptPixelDatas->iBpp    = iBpp;
	ptPixelDatas->iLineBytes    = ptPixelDatas->iWidth * ptPixelDatas->iBpp / 8;
    ptPixelDatas->iTotalBytes   = ptPixelDatas->iHeight * ptPixelDatas->iLineBytes;
	ptPixelDatas->aucPixelDatas = malloc(ptPixelDatas->iTotalBytes);
	if (NULL == ptPixelDatas->aucPixelDatas)
	{
		return -1;
	}

    pucDest = ptPixelDatas->aucPixelDatas;

	// 循环调用jpeg_read_scanlines来一行一行地获得解压的数据
	while (tDInfo.output_scanline < tDInfo.output_height) 
	{
        /* 得到一行数据,里面的颜色格式为0xRR, 0xGG, 0xBB */
		(void) jpeg_read_scanlines(&tDInfo, &aucLineBuffer, 1);

		// 转到ptPixelDatas去
		CovertOneLine(ptPixelDatas->iWidth, 24, ptPixelDatas->iBpp, aucLineBuffer, pucDest);
		pucDest += ptPixelDatas->iLineBytes;
	}
	
	free(aucLineBuffer);
	jpeg_finish_decompress(&tDInfo);
	jpeg_destroy_decompress(&tDInfo);

    return 0;
}
```

