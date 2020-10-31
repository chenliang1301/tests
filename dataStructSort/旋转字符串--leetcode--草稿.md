
#include <stdio.h>
#include <stdlib.h>
#include <string.h>

char* reverseLeftWords1(char* s, int n) {
	int len = strlen(s), i = 0;;
	char* ans = malloc(sizeof(char) * (len + 1));
	while (i < len) {
		*(ans++) = s[(n + i++) % len];
	}
	*ans = '\0';
	return ans - len;
}

char* reverseLeftWords(char* s, int n) {
	int len = strlen(s);
	char* p_i = (char*)malloc((len + 1) * sizeof(char));
	int i = 0;
	while (i < len)
	{
		*(p_i+i) = s[(n + (i++)) % len];
	}
	*(p_i + i) = '\0';
	return p_i;
}

int main()
{

	printf("%s\n",reverseLeftWords("abcdefgh", 2));
	return 0;
}
