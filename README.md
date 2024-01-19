using System.IO;
using NPOI.HSSF.UserModel;
using NPOI.SS.UserModel;
using NPOI.XSSF.UserModel;

class Program
{
    static void Main()
    {
        ConvertXlsxToXls("input.xlsx", "output.xls");
    }

    static void ConvertXlsxToXls(string xlsxFilePath, string xlsFilePath)
    {
        using (var fs = new FileStream(xlsxFilePath, FileMode.Open, FileAccess.Read))
        {
            var workbook = new XSSFWorkbook(fs);
            var newWorkbook = new HSSFWorkbook();

            for (int i = 0; i < workbook.NumberOfSheets; i++)
            {
                var sheet = newWorkbook.CreateSheet(workbook.GetSheetName(i));
                var oldSheet = (XSSFSheet)workbook.GetSheetAt(i);

                for (int j = 0; j <= oldSheet.LastRowNum; j++)
                {
                    var oldRow = (XSSFRow)oldSheet.GetRow(j);
                    var newRow = (HSSFRow)sheet.CreateRow(j);

                    if (oldRow != null)
                    {
                        for (int k = 0; k < oldRow.LastCellNum; k++)
                        {
                            var oldCell = (XSSFCell)oldRow.GetCell(k);
                            var newCell = (HSSFCell)newRow.CreateCell(k);

                            if (oldCell != null)
                            {
                                switch (oldCell.CellType)
                                {
                                    case CellType.Boolean:
                                        newCell.SetCellValue(oldCell.BooleanCellValue);
                                        break;
                                    case CellType.Numeric:
                                        newCell.SetCellValue(oldCell.NumericCellValue);
                                        break;
                                    case CellType.String:
                                        newCell.SetCellValue(oldCell.StringCellValue);
                                        break;
                                    case CellType.Formula:
                                        newCell.SetCellFormula(oldCell.CellFormula);
                                        break;
                                    default:
                                        // Xử lý các kiểu dữ liệu khác nếu cần
                                        break;
                                }
                            }
                        }
                    }
                }
            }

            using (var fileStream = new FileStream(xlsFilePath, FileMode.Create))
            {
                newWorkbook.Write(fileStream);
            }
        }

        Console.WriteLine("Chuyển đổi thành công từ .xlsx sang .xls");
    }
}
