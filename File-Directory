#include "stdafx.h"
#include "FileDirectory.h"
#include <iostream>
using namespace std;

FileDirectory::FileDirectory()
{
	//purpose: to initialize all entries in the fileDirectory and FAT16 to be 0, i.e. safe values.
	for (int i = 0; i < 4; i++)
	{
		for (int j = 0; j < 32; j++)
		{
			fileDirectory[i][j] = 0;
		}
	}
	for (int i = 0; i < 16; i++)
	{
		FAT16[i] = 0;
	}

}
bool FileDirectory::create(char fileName[], int numberBytes)
{
	//purpose: to create a file in the fileDirectory
	//(1)	to check if there is an unused entry in the File Directory,  (i.e.  the first character of the file name in the File Directory is zero). Return false if not true.
	int i = 0;
	for (i = 0; i < 4; i++)
	{
		if (fileDirectory[i][0] == 0)
		{
			break;
		}
	}
	//(2)	to check if there are enough unused clusters to store the file with the numberBytes.Return false if not true.
	if (i == 4)
	{
		return false;
	}
	else if (numberBytes <= freeClusters()*512)
	{
		return true;
	}
	else
	{
		return false;
	}
}
int FileDirectory::freeClusters()
{
	//purpose: to find the free clusters in the file directory
	int numberClusters = 0;
	for (int i = 1; i < 15; i++)
	{
		if (FAT16[i] == 0)
		{
			numberClusters++;
		}
	}
	return numberClusters;
}
int FileDirectory::checkFileDirectory(char fileName[])
{
	//purpose: to check if the file, fileName[] is in the directory, return false if it is not
	int i, j = 0;
	for (i = 0; i < 4; i++)
	{
		for (j = 0; j < 8; j++)
		{
			if (fileDirectory[i][j] != fileName[j])
			{
				break;
			}
		}
		if (j == 8)
		{
			break;
		}
	}
	return i;
}
bool FileDirectory::deleteFile(char fileName[])
{
	//purpose: to delete a file in the fileDirectory
	//(0)	to check if the file to be deleted, filename[], is in the Directory. If not, return false.
	int i, j = 0; 
	i = checkFileDirectory(fileName);
	unsigned short int endFile = FAT16[15];
	if (i == 4)
	{
		return false;
	}
	//(1)	to change the first character of the file name in the File Directory to be zero,
	fileDirectory[i][0] = 0;
	//(2)	to change all entries of the clusters of this file in the FAT to 0, i.e., unused.
	unsigned short int clusterAddress;
	unsigned short int nextClusterAddress;
	clusterAddress = fileDirectory[i][16]*256 + fileDirectory[i][15];
	for (i = 1; i < 15 && (FAT16[clusterAddress] != endFile); i++)
	{
		nextClusterAddress = FAT16[clusterAddress];
		FAT16[clusterAddress] = 0;
		clusterAddress = nextClusterAddress;
	}
	return true;
}
bool FileDirectory::read(char fileName[])
{
	//purpose: to read  of data from data[] array from the file with the specified file name.
	//(0)	to check if the file to be deleted, filename[], is in the Directory.If not, return false.
	int i, j = 0;
	i = checkFileDirectory(fileName);
	unsigned short int endFile = FAT16[15];
	if (i == 4)
	{
		return false;
	}
	//(1)	use the file name to get the file information from the File Directory, including date, time, number of bytes and the first cluster address,
	unsigned int fileLength = 0; //32-bit file length
	int year, month, day, hour, minute, second;
	for (j = 0; j < 8; j++)
	{
		fileDirectory[i][j] = fileName[j];
	}
	year = fileDirectory[i][12];
	month = fileDirectory[i][11] & 1100;
	day = fileDirectory[i][11] & 0011;
	hour = fileDirectory[i][13];
	minute = fileDirectory[i][14] & 1100;
	second = fileDirectory[i][14] & 0011;
	for (int index = 17; index < 21; index++)
	{
		fileLength = fileLength << 8;
		fileLength += fileDirectory[i][index];
	}
	//(2)	use the first cluster address to get all the cluster addresses of this file from the FAT - 16,
	unsigned short int clusterAddress;
	clusterAddress = fileDirectory[i][16] * 256 + fileDirectory[i][15];
	int index = 0;
	unsigned short int sectors[16];
	for (i = 1; i < 15 && (FAT16[clusterAddress] != endFile); i++)
	{
		sectors[index++] = clusterAddress;
		clusterAddress = FAT16[clusterAddress];
	}
	return true;
	//(3)	use all the cluster addresses to read the data from the disk / flash memory.
}
void FileDirectory::write(char fileName[], int numberBytes, char data[], int year, int month, int day, int hour, int minute, int second)
{
	//purpose: to write numberBytes bytes of data from data[] array into the file with the specified file name.
	//(1)	to look for the first unused entry(0 or 1) in the FAT - 16, and use it as the First Cluster Address.
	unsigned short int firstCluster = 1;
	int numOfClusters = numberBytes/500;
	int i, j;
	for (i = 1; i < 15; i++)
	{
		if (FAT16[i] == 0)
		{
			firstCluster = i;
			firstCluster = firstCluster;
			i++; numOfClusters--;
			break;
		}
	}
	//(2)	to look for the next unused entry(0 or 1) in the FAT - 16, and use it as the Next Cluster Address, and write its value into the FAT - 16.
	unsigned short int endFile = FAT16[15];
	unsigned short int nextCluster;
	for (i; i < 15; i++)
	{
		if (FAT16[i] == 0)
		{
			FAT16[firstCluster] = i;
			nextCluster = i;
			i++; numOfClusters--;
			break;
		}
	}
	//(3)	Repeat Step 2 until all clusters are found and the FAT - 16 is updated.
	for (i; i < 15; i++)
	{
		if (numOfClusters != 0)
		{
			if (FAT16[i] == 0)
			{
				FAT16[nextCluster] = i;
				nextCluster = i;
				numOfClusters--;
			}
		}
	}
	//(4)	to write / update the file name, extension, date, time, file length and first cluster address into the first unused entry in the File Directory,
	for (i = 0; i < 4; i++)
	{
		if (fileDirectory[i][0] == 0)
		{
			break;
		}
	}
	for (j = 0; j < 8; j++)
	{
		fileDirectory[i][j] = fileName[j];
	}
	int index = 0;
	for (j = 8; j < 11; j++)
	{
		fileDirectory[i][j] = data[index++];
	}
	fileDirectory[i][20] = (numberBytes&0xFF000000)>>24;
	fileDirectory[i][19] = (numberBytes&0x00FF0000)>>16;
	fileDirectory[i][18] = (numberBytes&0x0000FF00)>>8;
	fileDirectory[i][17] = numberBytes&0x000000FF;
	fileDirectory[i][15] = firstCluster;
	fileDirectory[i][12] = (year - 1980)<<1 | ((month)>>3);
	fileDirectory[i][11] = (month << 5) | day;
	fileDirectory[i][13] = hour << 3 | minute>>3;
	fileDirectory[i][14] = ((minute << 5) | second/2);
}
void FileDirectory::printClusters(char fileName[])
{
	//purpose: to print all the clusters of a file.
	//(1)	to check if the file to be printed, filename[], is in the Directory.If not, return false.
	int i;
	i = checkFileDirectory(fileName);
	//(2)	use the file name to get the file information from the File Directory, including the first cluster address,
	unsigned short int firstCluster;
	firstCluster = (fileDirectory[i][16]*256 + fileDirectory[i][15]);
	//(3)	use the first cluster address to get all cluster addresses from the FAT - 16,
	int index = 0;
	int numTimes = (fileDirectory[i][20]<<24 | fileDirectory[i][19]<<16 | fileDirectory[i][18]<<8 | fileDirectory[i][17])/500;
	unsigned short int endFile = FAT16[15];
	unsigned short int sectors[16];
	for (i = 1; i <= numTimes; i++)
	{
		sectors[index++] = firstCluster;
		firstCluster = FAT16[firstCluster];
	}
	index = 0;
	for (int j = 0; j < numTimes; j++)
	{
		cout << sectors[index++];
		if (j != numTimes - 1)
		{
			cout << "->";
		}
	}
	cout << endl;
}
void FileDirectory::printDirectory()
{
	//purpose: prints all the  files of the directory.
	//(1)	use the file name to get the file information from the File Directory, including the first cluster address,
	for (int i = 0; i < 4; i++)
	{
		for (int j = 0; j < 32; j++)
		{
			if (fileDirectory[i][0] == 0)
			{
				break;
			}
			if (j < 8)
			{
				cout << fileDirectory[i][j];
			}
			if (j == 11)
			{
				cout << ((fileDirectory[i][j] >> 5) + (fileDirectory[i][j + 1] & 00000001)) << "/" << ((fileDirectory[i][j]) & 0x1F) << "/";
			}
			if (j == 12)
			{
				cout << (((fileDirectory[i][j]) >> 1) + 1980) << " ";
			}
			if (j == 13)
			{
				cout << ((fileDirectory[i][j] & 0xF8) >> 3) << ":";
			}
			if (j == 14)
			{
				cout << (((fileDirectory[i][13] & 0x07) << 3) | (fileDirectory[i][14] & 0xE0) >> 5) << ":" << (fileDirectory[i][j] & 0x1F)*2 << " ";
			}
			if (j == 16)
			{
				cout << (fileDirectory[i][20] << 24 | fileDirectory[i][19] << 16 | fileDirectory[i][18] << 8 | fileDirectory[i][17]) << " ";
			}
			if (j == 21)
			{
				unsigned short int firstCluster;
				firstCluster = fileDirectory[i][15];
				cout << firstCluster << endl;
			}
		}
	}
	//(2)	use the first cluster address to get all cluster addresses from the FAT - 16,
}
