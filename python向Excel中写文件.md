xlwt 写文件
```
import xlwt

class WriteExcel():
    def wExcel(self, infile, outfile):
        readFile = open(infile, 'r')
        buf = readFile.read().split('\n')
        readFile.close()

        w = xlwt.Workbook()
        sheet = w.add_sheet('sheet1')
        for i in range(len(buf)):
            temp = buf[i].split('=')
            for j in range(len(temp)):
                sheet.write(i, j, temp[j].decode('utf-8'))
        w.save(outfile)

if __name__ == '__main__':
    f = WriteExcel()
    f.wExcel('test.txt', '1.xls')
```