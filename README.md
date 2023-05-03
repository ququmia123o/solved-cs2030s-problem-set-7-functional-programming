Download Link: https://assignmentchef.com/product/solved-cs2030s-problem-set-7-functional-programming
<br>



<ol>

 <li>Explain why the following code does not follow Functional Programming principles:</li>

</ol>

var still_alive = true;

while (still_alive) { wear_mask(); wash_hands(); keep_1m_apart(); test_covid();

}

<ol start="2">

 <li>We revisit Q4 of Recitation #5: study the classes QA, MCQ and TFQ in the Appendix. Since the checking of answers may throw exceptions, let’s use the Sandbox functor to deal with them. In main method of qaTest, 4 questions have been created and added to a list. Complete the code to use the Sandbox functor to display the question, check the answer, and add the result to the results</li>

</ol>

public class qaTest { static void displayResults(List&lt;Boolean&gt; rList) { int marks = 0; for (boolean a : rList) if (a) marks++;

System.out.println(String.format(“You got %d questions correct.”, marks));

}

public static void main(String[] args) {

List&lt;Boolean&gt; results = new ArrayList&lt;&gt;(); List&lt;QA&gt; questions = new ArrayList&lt;&gt;();

questions.add(new MCQ(“What is 1+1?”, ‘A’)); questions.add(new TFQ(“The sky is blue (T/F)”, ‘T’)); questions.add(new MCQ(“Which animal is an elephant?”, ‘C’)); questions.add(new TFQ(“A square is a circle (T/F)”, ‘F’));

for (QA q : questions) {

//Insert code here to use Sandbox to wrap each question,

//show it, get user input, and add the result to

//the results list above

} displayResults(results);

}

}

<ol start="3">

 <li>Lists are also functors! Unlike Optional or Sandbox, they wrap many values, not just one.</li>

</ol>

And although Java does not provide a map function, it is easy to do so.

&lt;T,U&gt; List&lt;U&gt; listMap(Function&lt;T,U&gt; f, List&lt;T&gt; list) { List&lt;U&gt; newList = new ArrayList&lt;&gt;(); for (T item : list)

newList.add(f.apply(item));

return newList;

}

It is also useful to define a filter function. This takes in a predicate, and returns a new list whose elements (from the original list) make the predicate true. Note that both map and filter create <em>new </em>lists, and leave their arguments unchanged, in true FP style.

&lt;T&gt; List&lt;T&gt; listFilter(Predicate&lt;T&gt; p, List&lt;T&gt; list) { List&lt;T&gt; newList = new ArrayList&lt;&gt;(); for (T item : list) if (p.test(item)) newList.add(item);

return newList;

}

With these, we may work on an entire collection of objects easily. For example, to convert all the words in a sentence to upper case, we may do the following:

List&lt;String&gt; intoWords(String sentence) {

List&lt;String&gt; result = new ArrayList&lt;&gt;();

//Add each word into the result list new Scanner(sentence).forEachRemaining(result::add); return result;

}

listMap(String::toUpperCase, intoWords(“The rain in Spain falls mainly in the plain”)); =&gt; [THE, RAIN, IN, SPAIN, FALLS, MAINLY, IN, THE, PLAIN] To upper-case only those words which have an even length:

boolean isEven(int n) { return n % 2 == 0; }

listMap(String::toUpperCase, listFilter(s -&gt; isEven(s.length()), intoWords(“The rain in Spain falls mainly in the plain”)));

=&gt; [RAIN, IN, MAINLY, IN]

<ul>

 <li>Define a function, stringReverse, that takes in a string s and returns a new string that is s reversed; eg. stringReverse(“hello”) returns “olleh”.</li>

 <li>Use this to reverse all the words in a sentence that contain the letter i. Try any sentence you like.</li>

 <li>Define a function, stringToList, that takes in a string s and returns a new ArrayList whose elements are strings containing each character of s. Example: stringToList(“hello”) returns [h, e, l, l, o].</li>

 <li>What happens when you do the following?</li>

</ul>

listMap(s -&gt; stringToList(s), intoWords(“CS2030 is great fun”));

<ul>

 <li>To avoid nested lists, write a listFlatmap function that, like listMap, takes in a function f and a list, and returns a new list whose elements are mapped by f, but flattens any nested list. Thus</li>

</ul>

listFlatMap(s -&gt; stringToList(s), intoWords(“CS2030 is great fun”));

=&gt; [C, S, 2, 0, 3, 0, i, s, g, r, e, a, t, f, u, n]

With listFlatmap, lists are also monads!




//*************** QA **************** import java.util.*; abstract class QA {

String question;     char correctAnswer;

public QA(String question, char ans) {         this.question = question;         this.correctAnswer = ans;     }         abstract boolean getAnswer();

public QA displayQuestion() {         System.out.println(this.question);         return this;     }

public char getInput() {         Scanner sc = new Scanner(System.in);                    System.out.printf(“=&gt; “);         char result = sc.next().charAt(0);         System.out.println(“”);         return result;     }

}

//*************** MCQ **************** import java.util.*;

class MCQ extends QA {     public MCQ(String question, char ans) {         super(question, ans);     }

@Override     boolean getAnswer() {         char answer = this.getInput();         if (answer &lt; ’A’ || answer &gt; ’E’) {             throw new InvalidMCQException(“Invalid MCQ answer”);         }         return this.correctAnswer == answer;     }

} //*************** TFQ **************** import java.util.*;

class TFQ extends QA {

public TFQ(String question, char ans) {         super(question, ans);     }

@Override     boolean getAnswer() {         char answer = this.getInput();                if (answer != ’T’ &amp;&amp; answer != ’F’) {             throw new InvalidTFQException(“Invalid TFQ answer”);         }         return this.correctAnswer == answer;     }

}

//*************** Sandbox.java **********

/**    Functor that separates exception handling from main processing.  */ import java.util.function.Predicate; import java.util.function.Function; import java.util.function.Consumer; import java.util.Objects;

/** Class Sandbox is a Functor, and is an immutable class.     It’s purpose is to separate exception handling from the main     sequence of mapping.  */ public final class Sandbox&lt;T&gt; {     private final T thing;     private final Exception exception;

private Sandbox(T thing, Exception ex) {         this.thing = thing;         this.exception = ex;     }         public static &lt;T&gt; Sandbox&lt;T&gt; make(T thing) {         return new Sandbox&lt;T&gt;(Objects.requireNonNull(thing), null);     }

public static &lt;T&gt; Sandbox&lt;T&gt; makeEmpty() {         return new Sandbox&lt;T&gt;(null, null);     }

public boolean isEmpty() {         return this.thing==null;     }

public boolean hasNoException() {         return this.exception == null;     }

public &lt;U&gt; Sandbox&lt;U&gt; map(Function&lt;T,U&gt; f) {         return map(f, “”);     }             public &lt;U&gt; Sandbox&lt;U&gt; map(Function&lt;T,U&gt; f, String errorMessage) {         if (this.isEmpty())             return new Sandbox&lt;U&gt;(null, this.exception);                 try {             return new Sandbox&lt;U&gt;(f.apply(this.thing), null);         }         catch (Exception ex) {             return new Sandbox&lt;U&gt;(null,                                   new Exception(errorMessage, ex));         }

}

/** consume calls the Consumer argument with the thing.

Exceptions, if any, are handled here.     */

public void consume(Consumer&lt;T&gt; eat) {         if (this.isEmpty() &amp;&amp; !this.hasNoException())             handleException();

if (!this.isEmpty() &amp;&amp; this.hasNoException())             eat.accept(this.thing);     }

private void handleException() {         System.err.printf(this.exception.getMessage());         Throwable t = this.exception.getCause();         if (t != null) {             String msg = t.getMessage();             if (msg != null)                 System.err.printf(“: %s”, msg);

}

System.err.println(“”);

}

}