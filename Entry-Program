#include "stdafx.h"
#include "FileDirectory.h"
#include <iostream>;
using namespace std;

int _tmain(int argc, _TCHAR* argv[])
{
	FileDirectory CPP;
	char data[8192];
	//Write a driver function, i.e.main(), to test this program by
	//1.	create and write a file, file1, of 1000 bytes,
	CPP.create("file1", 1000);
	CPP.write("file1", 1000, data, 2015, 2, 26, 2, 57, 24);
	CPP.printDirectory();
	CPP.printClusters("file1");
	//2.	create and write a file, file2, of 2000 bytes,
	CPP.create("file2", 2000);
	CPP.write("file2", 2000, data, 2015, 2, 26, 2, 58, 24);
	CPP.printDirectory();
	CPP.printClusters("file2");
	//3.	create and write a file, file3, of 3000 bytes,
	CPP.create("file3", 3000);
	CPP.write("file3", 3000, data, 2015, 2, 26, 2, 59, 24);
	CPP.printDirectory();
	CPP.printClusters("file3");
	//4.	create and write a file, file4, of 4000 bytes.
	if (CPP.create("file4", 4000))
	{
		CPP.write("file4", 4000, data, 2015, 2, 26, 2, 57, 24);
	}
	//5.	delete file3,
	CPP.deleteFile("file3");
	//6.	create and write a file, file4, of 4000 bytes.
	CPP.create("file4", 4000);
	CPP.write("file4", 4000, data, 2015, 2, 26, 3, 0, 24);
	CPP.printDirectory();
	CPP.printClusters("file4");
	return 0;
}
