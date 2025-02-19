\documentclass{article}
\usepackage{booktabs}
\usepackage{listings}
\usepackage{xcolor}
\usepackage[normalem]{ulem}
\usepackage{courier}

\lstset{
    basicstyle=\ttfamily,
    breaklines=true,
    frame=tb,
    captionpos=b,
    tabsize=4
}

\begin{document}

\section*{数据库表结构}
\begin{tabular}{ll}
\toprule
Column Name & Type \\
\midrule
id & int \\
salary & int \\
\bottomrule
\end{tabular}

\begin{itemize}
    \item id 是主键（唯一值列）
    \item 每行包含员工的薪资信息
\end{itemize}

\section*{问题要求}
编写查询找出第二高的不重复薪资。如果不存在第二高薪资，返回 null（Pandas 中返回 None）。

\section*{示例}

\subsection*{示例 1}
输入：
\begin{tabular}{|l|l|}
\hline
id & salary \\
\hline
1 & 100 \\
2 & 200 \\
3 & 300 \\
\hline
\end{tabular}

输出：
\begin{tabular}{|l|}
\hline
SecondHighestSalary \\
\hline
200 \\
\hline
\end{tabular}

\subsection*{示例 2}
输入：
\begin{tabular}{|l|l|}
\hline
id & salary \\
\hline
1 & 100 \\
\hline
\end{tabular}

输出：
\begin{tabular}{|l|}
\hline
SecondHighestSalary \\
\hline
null \\
\hline
\end{tabular}

\section*{解决方案}

\subsection*{方案 1}
\begin{lstlisting}[language=SQL]
SELECT
CASE
    WHEN count(*) = 0 Then null
    ELSE salary
END As SecondHighestSalary
FROM(
SELECT *,
ROW_NUMBER() OVER (ORDER BY salary DESC) as r
FROM (SELECT DISTINCT salary FROM Employee) as A) As B
where r = 2;
\end{lstlisting}

\subsection*{方案 2}
\begin{lstlisting}[language=SQL]
SELECT MAX(salary) AS SecondHighestSalary 
FROM Employee
WHERE salary < (SELECT salary FROM Employee ORDER BY salary DESC LIMIT 1);
\end{lstlisting}

\subsection*{方案 3}
\begin{lstlisting}[language=SQL]
SELECT
(SELECT DISTINCT Salary 
FROM Employee 
ORDER BY salary DESC 
LIMIT 1 OFFSET 1) 
AS SecondHighestSalary;
\end{lstlisting}

\section*{LIMIT 和 OFFSET 用法说明}
\begin{itemize}
    \item \textbf{LIMIT 1,3} 和 \textbf{LIMIT 3 OFFSET 1} 效果相同，都表示：
    \begin{itemize}
        \item 跳过1条数据
        \item 取后续3条数据（第2-4条）
    \end{itemize}

    \item \textbf{LIMIT 参数组合}：
    \begin{itemize}
        \item LIMIT x,y：跳过x条取y条
        \item LIMIT n：等价于 LIMIT 0,n
        \item LIMIT y OFFSET x：明确语法（推荐）
    \end{itemize}

    \item 典型用例：
    \begin{lstlisting}[language=SQL]
-- 取前三名
SELECT * FROM articles ORDER BY views DESC LIMIT 3;

-- 分页查询（每页10条）
SELECT * FROM users 
LIMIT 10 OFFSET 20; -- 第三页（跳过前20条）
    \end{lstlisting}
\end{itemize}

\end{document}
