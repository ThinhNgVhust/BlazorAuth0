```csharp
using System;
using System.Collections.Generic;
using System.IO;
using System.Text.RegularExpressions;

class Program
{
    static void Main(string[] args)
    {
        DateTime startDate = new DateTime(2023, 1, 1);
        DateTime endDate = new DateTime(2023, 1, 31);
        string logDirectory = "./logs";

        var logFiles = CollectLogFiles(startDate, endDate, logDirectory);
        var userAccessCount = CountUserAccess(logFiles);

        foreach (var user in userAccessCount)
        {
            Console.WriteLine($"ユーザーID: {user.Key}, アクセス数: {user.Value}");
        }
    }

    static List<string> CollectLogFiles(DateTime startDate, DateTime endDate, string logDirectory)
    {
        List<string> logFiles = new List<string>();
        for (DateTime currentDate = startDate; currentDate <= endDate; currentDate = currentDate.AddDays(1))
        {
            string logFileName = $"access-{currentDate.ToString("yyyy-MM-dd")}.log";
            string logFilePath = Path.Combine(logDirectory, logFileName);
            if (File.Exists(logFilePath))
            {
                logFiles.Add(logFilePath);
            }
        }
        return logFiles;
    }

    static Dictionary<string, int> CountUserAccess(List<string> logFiles)
    {
        Dictionary<string, int> userAccessCount = new Dictionary<string, int>();
        Regex userRegex = new Regex(@"ユーザー情報：(.*?),名前");

        foreach (var logFile in logFiles)
        {
            using (StreamReader reader = new StreamReader(logFile))
            {
                string line;
                while ((line = reader.ReadLine()) != null)
                {
                    Match match = userRegex.Match(line);
                    if (match.Success)
                    {
                        string userId = match.Groups[1].Value;
                        if (userAccessCount.ContainsKey(userId))
                        {
                            userAccessCount[userId]++;
                        }
                        else
                        {
                            userAccessCount.Add(userId, 1);
                        }
                    }
                }
            }
        }
        return userAccessCount;
    }
}
```
