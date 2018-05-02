---
title: "c#"
date: 2015-01-16
---


# Regex

[stackoverflow](http://stackoverflow.com/questions/1811183/how-to-extract-the-contents-of-square-brackets-in-a-string-of-text-in-c-sharp-us)で見たコードの詳細を調べる

コード


~~~ csharp

string s = "test [4df] test [5y" + Environment.NewLine + "u] test [6nf]";

ICollection<string> matches =
  Regex.Matches(s.Replace(Environment.NewLine, ""), @"\[([^]]*)\]")
    .Cast<Match>()
    .Select(x => x.Groups[1].Value)
    .ToList();

foreach (string match in matches)
  Console.WriteLine(match);

~~~

正規表現の意味

~~~
  \[   : Match a literal [
  (    : Start a new group, match.Groups[1]
  [^]] : Match any character except ]
  *    : 0 or more of the above
  )    : Close the group
  \]   : Literal ]
~~~


自分の場合やりたかったのは
「さむい(小並感)」みたいな文字列から「小並感」を抽出したかった
あるいは「ほほほ(へへへ(ふふふ))」から「へへへ(ふふふ)」を抽出

こうした
ちょっと不完全か

~~~ csharp

public string TrimBrackets(string param)
{
  if (string.IsNullOrEmpty(param)) return param;
  var regex = new Regex(@"\(([^)]*)\)");
  var match =  regex.Match(param);
  if (!match.Success) return param;
  return match.Groups[1].Value;
}

~~~


# Linq

重複チェックのようなもの、かいた


~~~ csharp

var check = units
  .GroupBy(x => new { x.cd, x.label_e })
  .GroupBy(x => new { x.Key.cd })
  .Select(x => new { cd = x.Key, count = x.Count()});

var result = check.Any(x => x.count > 1);

if (result)
{
  check.Where(x => x.count > 1)
  .ToList()
  .ForEach(x =>
    {
      var cd = x.cd;
      logger.Info("指標違い：" + cd);
      });
    }
return result;

~~~


