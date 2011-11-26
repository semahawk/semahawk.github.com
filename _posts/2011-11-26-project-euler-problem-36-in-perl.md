---
layout: post
title: "Project Euler: Problem 36 in Perl"
desc: "A quick solution to Project Euler's 36th problem using Perl. Don't look here unless you really want to. Spoilers are baad."
---
## What's it about
{% highlight text %}
The decimal number, 585 = 10010010012 (binary), is palindromic in both bases.

Find the sum of all numbers, less than one million, which are palindromic in base 10 and base 2.

(Please note that the palindromic number, in either base, may not include leading zeros.)
{% endhighlight %}


## Hints
<dt>Palindromic</dt>
<dd>A number which is exactly the same reading from left to right, and right to left, eg. 14641.</dd>
You would definitly need a function to convert from decimal to binary (or other way round :D). Then it's just matter of looping through one million, and checking if number completes the statement.

__Easy as carrot juice!__

# The solution <small>spoiler alert!</small>
### Bitizing
For first we need a function that converts from decimal, to binary. I called main `bitize`. *wink*.

{% highlight perl %}
sub bitize {
	# array of bits
	my @bits = qw();
	# shift deletes the first argument from $_ array, and returns it. 
	my $num = shift;

	while ($num >= 1){
		# even
		if ($num % 2 == 0){
			push(@bits, 0);
		# odd
		} else {
			push(@bits, 1);
		}
		
		$num /= 2;
	}
	
	reverse @bits;
}
{% endhighlight %}

Okay, we're __one step closer__!

### Looping
{% highlight perl %}
# this is the sum, the answer that is
my $sum = 0;

# looping all the way to the million.
for ($n = 1; $n < 1000000; $n++){
	# here we check if the number is palindromic in base 10
    if (reverse($n) == $n &&
        # and here if it is in base 2
        bitize($n) == reverse(bitize($n))){
		# gotcha!
		$sum += $n;
	}
}
{% endhighlight %}

### Winning!
Now there is only __one__ thing left to do.

{% highlight perl %}
print "thee answah: $sum\n";
{% endhighlight %}

# Problem 36 beaten!
