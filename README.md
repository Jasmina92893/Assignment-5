import java.util.Stack;

public class StringProcessor {

    /**
     * Checks if the provided password is strong.
     * A strong password must contain at least one uppercase letter, one lowercase letter,
     * one digit, and one special symbol.
     *
     * @param password the string representing the password to check
     * @return true if the password is strong, false otherwise
     */
    public boolean isStrongPassword(String password) {
        if (password == null || password.length() < 8) {
            return false;
        }

        boolean hasUppercase = false;
        boolean hasLowercase = false;
        boolean hasDigit = false;
        boolean hasSpecialChar = false;

        for (char ch : password.toCharArray()) {
            if (Character.isUpperCase(ch)) {
                hasUppercase = true;
            } else if (Character.isLowerCase(ch)) {
                hasLowercase = true;
            } else if (Character.isDigit(ch)) {
                hasDigit = true;
            } else if (!Character.isLetterOrDigit(ch)) {
                hasSpecialChar = true;
            }
        }

        return hasUppercase && hasLowercase && hasDigit && hasSpecialChar;
    }

    /**
     * Counts the number of digits in the given sentence.
     *
     * @param sentence the string to count digits from
     * @return the count of digits in the string
     */
    public int calculateDigits(String sentence) {
        if (sentence == null || sentence.isEmpty()) {
            return 0;
        }

        int digitCount = 0;
        for (char ch : sentence.toCharArray()) {
            if (Character.isDigit(ch)) {
                digitCount++;
            }
        }

        return digitCount;
    }

    /**
     * Calculates the number of words in the given sentence.
     * Words are considered to be separated by spaces.
     *
     * @param sentence the string representing the sentence
     * @return the count of words in the sentence
     */
    public int calculateWords(String sentence) {
        if (sentence == null || sentence.trim().isEmpty()) {
            return 0;
        }

        String[] words = sentence.trim().split("\\s+");
        return words.length;
    }

    /**
     * Evaluates the given mathematical expression in the form of a string.
     * The expression contains numbers, arithmetic operations, and brackets.
     * Assumes the expression is well-formed.
     *
     * @param expression the string containing the mathematical expression
     * @return the result of the evaluated expression
     */
    public double calculateExpression(String expression) {
        try {
            return evaluateExpression(expression);
        } catch (Exception e) {
            System.err.println("Invalid expression: " + e.getMessage());
            return 0;
        }
    }

    /**
     * Helper method to evaluate a mathematical expression using two stacks.
     * One stack stores numbers, the other stores operators.
     *
     * @param expression the mathematical expression to evaluate
     * @return the result of the expression
     */
    private double evaluateExpression(String expression) {
        Stack<Double> numbers = new Stack<>();
        Stack<Character> operators = new Stack<>();

        for (int i = 0; i < expression.length(); i++) {
            char ch = expression.charAt(i);

            if (Character.isDigit(ch)) {
                // Reading the complete number
                StringBuilder sb = new StringBuilder();
                while (i < expression.length() && (Character.isDigit(expression.charAt(i)) || expression.charAt(i) == '.')) {
                    sb.append(expression.charAt(i++));
                }
                i--; // To adjust for the loop increment
                numbers.push(Double.parseDouble(sb.toString()));
            } else if (ch == '(') {
                operators.push(ch);
            } else if (ch == ')') {
                while (operators.peek() != '(') {
                    numbers.push(applyOperation(operators.pop(), numbers.pop(), numbers.pop()));
                }
                operators.pop(); // Pop '('
            } else if (ch == '+' || ch == '-' || ch == '*' || ch == '/') {
                // Check for precedence and apply any pending operations
                while (!operators.isEmpty() && hasPrecedence(ch, operators.peek())) {
                    numbers.push(applyOperation(operators.pop(), numbers.pop(), numbers.pop()));
                }
                operators.push(ch);
            }
        }

        // Apply the remaining operations
        while (!operators.isEmpty()) {
            numbers.push(applyOperation(operators.pop(), numbers.pop(), numbers.pop()));
        }

        return numbers.pop();
    }

    /**
     * Determines if the current operator has precedence over the operator on the stack.
     *
     * @param currentOp the current operator
     * @param stackOp the operator on the stack
     * @return true if stackOp has higher or equal precedence than currentOp
     */
    private boolean hasPrecedence(char currentOp, char stackOp) {
        if (stackOp == '(' || stackOp == ')') {
            return false;
        }
        if ((currentOp == '*' || currentOp == '/') && (stackOp == '+' || stackOp == '-')) {
            return false;
        }
        return true;
    }

    /**
     * Applies the operation between two numbers.
     *
     * @param op the operator
     * @param b the second operand
     * @param a the first operand
     * @return the result of the operation
     */
    private double applyOperation(char op, double b, double a) {
        switch (op) {
            case '+':
                return a + b;
            case '-':
                return a - b;
            case '*':
                return a * b;
            case '/':
                if (b == 0) {
                    throw new ArithmeticException("Division by zero");
                }
                return a / b;
            default:
                throw new IllegalArgumentException("Unknown operator: " + op);
        }
    }

    public static void main(String[] args) {
        StringProcessor processor = new StringProcessor();

        // Testing Password Strength
        System.out.println("Password Tests:");
        System.out.println(processor.isStrongPassword("Strong@123"));   // true
        System.out.println(processor.isStrongPassword("weakpass"));     // false
        System.out.println(processor.isStrongPassword("123456789"));    // false
        System.out.println(processor.isStrongPassword("Valid#1"));      // true
        System.out.println(processor.isStrongPassword("Invalid1"));     // false

        // Testing Digit Count
        System.out.println("\nDigit Count Tests:");
        System.out.println(processor.calculateDigits("This has 2 digits: 34"));  // 2
        System.out.println(processor.calculateDigits("No digits here"));         // 0
        System.out.println(processor.calculateDigits("1234567890"));             // 10
        System.out.println(processor.calculateDigits("4 numbers in 1234"));      // 4
        System.out.println(processor.calculateDigits(""));                       // 0

        // Testing Word Count
        System.out.println("\nWord Count Tests:");
        System.out.println(processor.calculateWords("Hello world"));             // 2
        System.out.println(processor.calculateWords("This is a test case"));     // 5
        System.out.println(processor.calculateWords("Singleword"));              // 1
        System.out.println(processor.calculateWords("   "));                     // 0
        System.out.println(processor.calculateWords("Three words here"));        // 3

        // Testing Expression Evaluation
        System.out.println("\nExpression Evaluation Tests:");
        System.out.println(processor.calculateExpression("3 + 5"));              // 8.0
        System.out.println(processor.calculateExpression("10 / 2"));             // 5.0
        System.out.println(processor.calculateExpression("(2 + 3) * 4"));        // 20.0
        System.out.println(processor.calculateExpression("100 - 50 + 25"));      // 75.0
        System.out.println(processor.calculateExpression("3.5 * 2"));            // 7.0
    }
}
