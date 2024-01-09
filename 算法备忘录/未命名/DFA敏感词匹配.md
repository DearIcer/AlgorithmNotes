``` csharp
using System;
using System.Collections.Generic;
using System.IO;

public class DFA
{
    private HashSet<string> states;  // 状态集合
    private HashSet<char> alphabet;  // 输入字母表
    private Dictionary<string, Dictionary<char, string>> transitions;  // 状态转换函数
    private string initialState;  // 初始状态
    private HashSet<string> acceptStates;  // 接受状态集合
    private HashSet<string> lexicon;  // 词库集合

    public DFA(string lexiconFilePath)
    {
        // 初始化 DFA
        states = new HashSet<string> { "q0" };
        alphabet = new HashSet<char>();
        transitions = new Dictionary<string, Dictionary<char, string>>
        {
            { "q0", new Dictionary<char, string>() }
        };
        initialState = "q0";
        acceptStates = new HashSet<string>();

        // 读取词库文件
        lexicon = new HashSet<string>();
        try
        {
            string[] lines = File.ReadAllLines(lexiconFilePath);
            foreach (string line in lines)
            {
                string pattern = line.Trim();
                lexicon.Add(pattern);

                AddPatternToDFA(pattern);  // 将模式添加到 DFA 的状态转换函数中
            }
        }
        catch (Exception e)
        {
            Console.WriteLine("无法读取词库文件：" + e.Message);
        }
    }

    private void AddPatternToDFA(string pattern)
    {
        string currentState = initialState;

        foreach (char c in pattern)
        {
            if (!transitions.ContainsKey(currentState))
                transitions[currentState] = new Dictionary<char, string>();

            if (!transitions[currentState].ContainsKey(c))
            {
                string nextState = "q" + (states.Count);
                states.Add(nextState);
                transitions[currentState][c] = nextState;
            }

            currentState = transitions[currentState][c];
        }

        acceptStates.Add(currentState);
    }

    public bool Match(string inputString)
    {
        string currentState = initialState;

        foreach (char c in inputString)
        {
            if (transitions.ContainsKey(currentState) && transitions[currentState].ContainsKey(c))
                currentState = transitions[currentState][c];
            else
                return false;  // 如果当前状态无法根据输入字符进行转换，则返回False
        }

        return acceptStates.Contains(currentState);  // 如果最终状态在接受状态集合中，则返回True，否则返回False
    }
}

class Program
{
    static void Main(string[] args)
    {
        string lexiconFilePath = "C:\\Users\\Ice\\Desktop\\屏蔽词.txt";  // 词库文件路径
        DFA dfa = new DFA(lexiconFilePath);
        Console.Write("请输入一个字符串: ");
        string inputString = Console.ReadLine();

        if (dfa.Match(inputString))
            Console.WriteLine("输入字符串中匹配到词库中的模式");
        else
            Console.WriteLine("输入字符串中未匹配到词库中的模式");

        Console.ReadLine();
    }
}

```