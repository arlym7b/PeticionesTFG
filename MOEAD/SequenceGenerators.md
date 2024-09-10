## IntegerBoundedSequenceGenerator
### Petición
```
Translate this Java class to Julia:
/**
 * This class generates a bounded sequence of consecutive integer numbers. When the last number is generated, the
 * sequence starts again.
 *
 * @author Antonio J. Nebro
 */
public class IntegerBoundedSequenceGenerator implements SequenceGenerator<Integer> {
  private int index;
  private int size ;

  public IntegerBoundedSequenceGenerator(int size) {
    Check.that(size > 0, "Size " + size + " is not a positive number greater than zero");
    this.size = size ;
    index = 0;
  }

  @Override
  public Integer getValue() {
    return index ;
  }

  @Override
  public void generateNext() {
    index++;
    if (index == size) {
      index = 0;
    }
  }

  @Override
  public int getSequenceLength() {
    return size ;
  }
}
```
### ChatGPT
```
"""
This struct generates a bounded sequence of consecutive integer numbers. When the last number is generated,
the sequence starts again.

@author Antonio J. Nebro
"""
struct IntegerBoundedSequenceGenerator
    index::Int
    size::Int

    function IntegerBoundedSequenceGenerator(size::Int)
        @assert size > 0 "Size $size is not a positive number greater than zero"
        new(0, size)
    end
end

"""
Returns the current value in the sequence.
"""
function getValue(generator::IntegerBoundedSequenceGenerator)::Int
    return generator.index
end

"""
Generates the next value in the sequence. Wraps around to 0 when reaching the specified size.
"""
function generateNext!(generator::IntegerBoundedSequenceGenerator)
    generator.index += 1
    if generator.index == generator.size
        generator.index = 0
    end
end

"""
Returns the length of the sequence.
"""
function getSequenceLength(generator::IntegerBoundedSequenceGenerator)::Int
    return generator.size
end

```
## IntegerPermutationGenerator
### Petición
```
Translate this Java class to Julia:
public class IntegerPermutationGenerator implements SequenceGenerator<Integer> {
  private int[] sequence;
  private int index;
  private int size ;

  public IntegerPermutationGenerator(int size) {
    Check.that(size > 0, "Size " + size + " is not a positive number greater than zero");
    this.size = size ;
    sequence = randomPermutation(size) ;

    index = 0;
  }

  @Override
  public Integer getValue() {
    return sequence[index];
  }

  @Override
  public void generateNext() {
    index++;
    if (index == sequence.length) {
      sequence = randomPermutation(size) ;
      index = 0;
    }
  }

  private int[] randomPermutation(int size) {
    int[] permutation = new int[size] ;
    JMetalRandom randomGenerator = JMetalRandom.getInstance() ;
    int[] index = new int[size];
    boolean[] flag = new boolean[size];

    for (int n = 0; n < size; n++) {
      index[n] = n;
      flag[n] = true;
    }

    int num = 0;
    while (num < size) {
      int start = randomGenerator.nextInt(0, size - 1);
      while (true) {
        if (flag[start]) {
          permutation[num] = index[start];
          flag[start] = false;
          num++;
          break;
        }
        if (start == (size - 1)) {
          start = 0;
        } else {
          start++;
        }
      }
    }
    return permutation ;
  }

  @Override
  public int getSequenceLength() {
    return size ;
  }
}
```
### ChatGPT
```
"""
This struct generates a sequence of randomly permuted integers from 0 to size-1. When the sequence is exhausted,
a new random permutation is generated.

@author Antonio J. Nebro
"""
struct IntegerPermutationGenerator
    sequence::Vector{Int}
    index::Int
    size::Int

    function IntegerPermutationGenerator(size::Int)
        @assert size > 0 "Size $size is not a positive number greater than zero"
        new(randomPermutation(size), 0, size)
    end
end

"""
Returns the current value in the sequence.
"""
function getValue(generator::IntegerPermutationGenerator)::Int
    return generator.sequence[generator.index + 1]  # Julia is 1-indexed
end

"""
Generates the next value in the sequence. If the sequence is exhausted, a new random permutation is generated.
"""
function generateNext!(generator::IntegerPermutationGenerator)
    generator.index += 1
    if generator.index == length(generator.sequence)
        generator.sequence = randomPermutation(generator.size)
        generator.index = 0
    end
end

"""
Generates a random permutation of integers from 0 to size-1.
"""
function randomPermutation(size::Int)::Vector{Int}
    permutation = collect(0:(size-1))  # Create a list [0, 1, 2, ..., size-1]
    flag = fill(true, size)
    result = Vector{Int}(undef, size)
    
    randomGenerator = RandomDevice()  # Julia uses Random for random number generation

    num = 0
    while num < size
        start = rand(1:size)  # Generate a random index between 1 and size (1-indexed in Julia)
        while true
            if flag[start]
                result[num + 1] = permutation[start]  # Assign and mark used (Julia is 1-indexed)
                flag[start] = false
                num += 1
                break
            end
            start = (start == size) ? 1 : start + 1
        end
    end

    return result
end

"""
Returns the length of the sequence.
"""
function getSequenceLength(generator::IntegerPermutationGenerator)::Int
    return generator.size
end
```