# Simple WPF Calculator
CalculatorWpfMVVM is a simple calculator built using the Model-View-ViewModel (MVVM) pattern in WPF. <br>
This project was originally created by dariuszdbr, and can be found in this [link](https://github.com/dariuszdbr/CalculatorWpfMVVM). <br>
The main logic of this project is implemented in the `CalculatorModel.cs` and it uses the [mXparser](https://mathparser.org/) library to handle mathematical operations.
<br>
<br>
## Modifications
My main goal was to implement a power operator. However, I have also fixed the bug of bracket insertion as the first element of expression, and implementing the functionality of percentage operator.
<br>

To implement power functioanlity, I just modified the regular expression from `[+\-*/%,]` to  `[+\-*/^,]` so that it recognizes and calculates the power symbol ('^'). <br>
The reason that I have removed percentage symbol ('%') from this regular expression is that percentage action is defined in the `Operations` enum and it should be handled in this part.
<br>
<br>
To implement the percentage operation, I added a function as below:
```
private void Percentage()
{
    // Use regular expression to find the last number in the expression
    var match = Regex.Match(Expression, @"\d*\.?\d+$");
    if (match.Success)
    {
        double lastNumber = double.Parse(match.Value);
        double percentage = lastNumber / 100;

        // Remove the last number from the expression and add the percentage
        Expression = Expression.Remove(match.Index, match.Length);
        Expression += percentage;

        Result = CalculateExpression();
    }
}
```
This function get the last number of the expression and perform a percentage operation on it. <br>
For example, by passing this expression: `10+15.15%`, you would receive 10.1515 as the final result of your expression.!
<br><br>
There was also a bug when we insert bracket as the first element of our expression, which I fixed by adding the following condition in the `AddBracket()` function: <br>
 ```
if(Expression.Length == 0)
{
    Expression = OpenBracketSign;
    _encloseBracketStack.Push(EncloseBracketSign);
    Result = string.Empty;
}
else if (!_encloseBracketStack.Any())
{
    Expression += Regex.IsMatch(Expression.Last().ToString(), @"\d") ? $"*{OpenBracketSign}" : OpenBracketSign;
    _encloseBracketStack.Push(EncloseBracketSign);
    Result = string.Empty;
}
else
{
    Expression += _encloseBracketStack.Pop();
    Result = CalculateExpression();
}
 ```

## Preview
In the following image, you can see a demo of power function: <br>
[power_demo](https://github.com/alinakhaee/CalculatorWpfMVVM/assets/47724896/4991b033-3d21-4068-ba2f-3ac9e16e9eaa)
