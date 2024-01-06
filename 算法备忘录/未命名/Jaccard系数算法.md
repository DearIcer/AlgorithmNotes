``` csharp
string str1 = "hello world";
string str2 = "hello world!";

var set1 = str1.ToLower().ToHashSet();
var set2 = str2.ToLower().ToHashSet();

int intersection = set1.Intersect(set2).Count();
int union = set1.Union(set2).Count();

double similarity = (double)intersection / union;

Console.WriteLine(similarity);
```