# RWA1: Subasi2016
## Petición
```
This is the Java implementation for the Fonseca problem:
package org.uma.jmetal.problem.multiobjective;

import java.util.ArrayList;
import java.util.List;
import org.uma.jmetal.problem.doubleproblem.impl.AbstractDoubleProblem;
import org.uma.jmetal.solution.doublesolution.DoubleSolution;

/**
 * Class representing problem Fonseca
 */
@SuppressWarnings("serial")
public class Fonseca extends AbstractDoubleProblem {

  /**
   * Constructor
   */
  public Fonseca() {
    int numberOfVariables = 3;
    numberOfObjectives(2);
    numberOfConstraints(0);
    name("Fonseca");

    List<Double> lowerLimit = new ArrayList<>(numberOfVariables);
    List<Double> upperLimit = new ArrayList<>(numberOfVariables);

    for (int i = 0; i < numberOfVariables; i++) {
      lowerLimit.add(-4.0);
      upperLimit.add(4.0);
    }

    variableBounds(lowerLimit, upperLimit);
  }

  /**
   * Evaluate() method
   */
  @Override
  public DoubleSolution evaluate(DoubleSolution solution) {
    int numberOfVariables = numberOfVariables();

    double[] f = new double[solution.objectives().length];
    double[] x = new double[numberOfVariables];

    for (int i = 0; i < numberOfVariables; i++) {
      x[i] = solution.variables().get(i);
    }

    double sum1 = 0.0;
    for (int i = 0; i < numberOfVariables; i++) {
      sum1 += StrictMath.pow(x[i] - (1.0 / StrictMath.sqrt(numberOfVariables)), 2.0);
    }
    double exp1 = StrictMath.exp((-1.0) * sum1);
    f[0] = 1 - exp1;

    double sum2 = 0.0;
    for (int i = 0; i < numberOfVariables; i++) {
      sum2 += StrictMath.pow(x[i] + (1.0 / StrictMath.sqrt(numberOfVariables)), 2.0);
    }
    double exp2 = StrictMath.exp((-1.0) * sum2);
    f[1] = 1 - exp2;

    solution.objectives()[0] = f[0];
    solution.objectives()[1] = f[1];

    return solution;
  }
}


And this is how it is implemented in Julia:
function fonseca()
  problem = ContinuousProblem{Float64}("Fonseca")

  numberOfVariables = 3
  for _ in 1:numberOfVariables
    addVariable(problem, Bounds{Float64}(-4.0, 4.0))
  end

  f1 = x -> begin
    sum1 = 0.0
    for i in range(1, numberOfVariables)
      sum1 += ^(x[i] - (1.0 / sqrt(1.0 * numberOfVariables)), 2.0)
    end
    return 1.0 - exp(-1.0 * sum1)
  end

  f2 = x -> begin
    sum2 = 0.0
    for i in range(1, numberOfVariables)
      sum2 += ^(x[i] + (1.0 / sqrt(1.0 * numberOfVariables)), 2.0)
    end

    return 1.0 - exp(-1.0 * sum2)
  end

  addObjective(problem, f1)
  addObjective(problem, f2)

  return problem
end

Translate the Subasi2016 problem to Julia, which implementation in Java is this:
package org.uma.jmetal.problem.multiobjective.rwa;

import java.util.List;
import org.uma.jmetal.problem.doubleproblem.impl.AbstractDoubleProblem;
import org.uma.jmetal.solution.doublesolution.DoubleSolution;

/**
 * Problem Subasi2016 (RWA1) described in the paper "Engineering applications of
 * multi-objective evolutionary algorithms: A test suite of box-constrained real-world
 * problems". DOI: https://doi.org/10.1016/j.engappai.2023.106192
 */
public class Subasi2016 extends AbstractDoubleProblem {
  public Subasi2016() {
    numberOfObjectives(2);
    List<Double> lowerLimit = List.of(20.0, 6.0, 20.0, 0.0, 8000.0) ;
    List<Double> upperLimit = List.of(60.0, 15.0, 40.0, 30.0, 25000.0) ;
    name("Subasi2016");

    variableBounds(lowerLimit, upperLimit);
  }

  @Override
  public DoubleSolution evaluate(DoubleSolution solution) {
    double H = solution.variables().get(0);
    double t = solution.variables().get(1);
    double Sy = solution.variables().get(2);
    double theta = solution.variables().get(3);
    double Re = solution.variables().get(4);

    double f;
    double Nu;

    Nu = 89.027 + 0.300 * H - 0.096 * t - 1.124 * Sy - 0.968 * theta
        + 4.148 * 10e-3 * Re + 0.0464 * H * t - 0.0244 * H * Sy
        + 0.0159 * H * theta + 4.151 * 10e-5 * H * Re + 0.1111 * t * Sy
        - 4.121 * 10e-5 * Sy * Re + 4.192 * 10e-5 * theta * Re;

    f = 0.4753 - 0.0181 * H + 0.0420 * t + 5.481 * 10e-3 * Sy - 0.0191 * theta
        - 3.416 * 10e-6 * Re - 8.851 * 10e-4 * H * Sy
        + 8.702 * 10e-4 * H * theta + 1.536 * 10e-3 * t * theta
        - 2.761 * 10e-6 * t * Re - 4.400 * 10e-4 * Sy * theta
        + 9.714 * 10e-7 * Sy * Re + 6.777 * 10e-4 * H * H;

    solution.objectives()[0] = -Nu;
    solution.objectives()[1] = f;

    return solution ;
  }
}
```
## ChatGPT
```
function subasi2016()
    problem = ContinuousProblem{Float64}("Subasi2016")

    lowerLimit = [20.0, 6.0, 20.0, 0.0, 8000.0]
    upperLimit = [60.0, 15.0, 40.0, 30.0, 25000.0]
    
    numberOfVariables = length(lowerLimit)

    for i in 1:numberOfVariables
        addVariable(problem, Bounds{Float64}(lowerLimit[i], upperLimit[i]))
    end

    f1 = x -> begin
        H, t, Sy, theta, Re = x
        Nu = 89.027 + 0.300 * H - 0.096 * t - 1.124 * Sy - 0.968 * theta +
            4.148 * 10e-3 * Re + 0.0464 * H * t - 0.0244 * H * Sy + 
            0.0159 * H * theta + 4.151 * 10e-5 * H * Re + 0.1111 * t * Sy - 
            4.121 * 10e-5 * Sy * Re + 4.192 * 10e-5 * theta * Re
        return -Nu
    end

    f2 = x -> begin
        H, t, Sy, theta, Re = x
        f = 0.4753 - 0.0181 * H + 0.0420 * t + 5.481 * 10e-3 * Sy - 0.0191 * theta -
            3.416 * 10e-6 * Re - 8.851 * 10e-4 * H * Sy + 
            8.702 * 10e-4 * H * theta + 1.536 * 10e-3 * t * theta - 
            2.761 * 10e-6 * t * Re - 4.400 * 10e-4 * Sy * theta +
            9.714 * 10e-7 * Sy * Re + 6.777 * 10e-4 * H * H
        return f
    end

    addObjective(problem, f1)
    addObjective(problem, f2)

    return problem
end

```
## Gemini
```
function subasi2016()
  problem = ContinuousProblem{Float64}("Subasi2016")

  # Define number of variables and bounds
  n_vars = 5
  lower_bounds = [20.0, 6.0, 20.0, 0.0, 8000.0]
  upper_bounds = [60.0, 15.0, 40.0, 30.0, 25000.0]
  for i in 1:n_vars
    addVariable(problem, Bounds{Float64}(lower_bounds[i], upper_bounds[i]))
  end

  # Define objective functions
  f1 = x -> begin
    H, t, Sy, theta, Re = x
    Nu = 89.027 + 0.300 * H - 0.096 * t - 1.124 * Sy - 0.968 * theta
         + 4.148 * 10e-3 * Re + 0.0464 * H * t - 0.0244 * H * Sy
         + 0.0159 * H * theta + 4.151 * 10e-5 * H * Re + 0.1111 * t * Sy
         - 4.121 * 10e-5 * Sy * Re + 4.192 * 10e-5 * theta * Re
    return -Nu
  end

  f2 = x -> begin
    H, t, Sy, theta, Re = x
    f = 0.4753 - 0.0181 * H + 0.0420 * t + 5.481 * 10e-3 * Sy - 0.0191 * theta
       - 3.416 * 10e-6 * Re - 8.851 * 10e-4 * H * Sy
       + 8.702 * 10e-4 * H * theta + 1.536 * 10e-3 * t * theta
       - 2.761 * 10e-6 * t * Re - 4.400 * 10e-4 * Sy * theta
       + 9.714 * 10e-7 * Sy * Re + 6.777 * 10e-4 * H * H
    return f
  end

  # Add objectives
  addObjective(problem, f1)
  addObjective(problem, f2)

  return problem
end
```
# RWA2: Liao2008
## Petición
```
This is the Java implementation for the Fonseca problem:
package org.uma.jmetal.problem.multiobjective;

import java.util.ArrayList;
import java.util.List;
import org.uma.jmetal.problem.doubleproblem.impl.AbstractDoubleProblem;
import org.uma.jmetal.solution.doublesolution.DoubleSolution;

/**
 * Class representing problem Fonseca
 */
@SuppressWarnings("serial")
public class Fonseca extends AbstractDoubleProblem {

  /**
   * Constructor
   */
  public Fonseca() {
    int numberOfVariables = 3;
    numberOfObjectives(2);
    numberOfConstraints(0);
    name("Fonseca");

    List<Double> lowerLimit = new ArrayList<>(numberOfVariables);
    List<Double> upperLimit = new ArrayList<>(numberOfVariables);

    for (int i = 0; i < numberOfVariables; i++) {
      lowerLimit.add(-4.0);
      upperLimit.add(4.0);
    }

    variableBounds(lowerLimit, upperLimit);
  }

  /**
   * Evaluate() method
   */
  @Override
  public DoubleSolution evaluate(DoubleSolution solution) {
    int numberOfVariables = numberOfVariables();

    double[] f = new double[solution.objectives().length];
    double[] x = new double[numberOfVariables];

    for (int i = 0; i < numberOfVariables; i++) {
      x[i] = solution.variables().get(i);
    }

    double sum1 = 0.0;
    for (int i = 0; i < numberOfVariables; i++) {
      sum1 += StrictMath.pow(x[i] - (1.0 / StrictMath.sqrt(numberOfVariables)), 2.0);
    }
    double exp1 = StrictMath.exp((-1.0) * sum1);
    f[0] = 1 - exp1;

    double sum2 = 0.0;
    for (int i = 0; i < numberOfVariables; i++) {
      sum2 += StrictMath.pow(x[i] + (1.0 / StrictMath.sqrt(numberOfVariables)), 2.0);
    }
    double exp2 = StrictMath.exp((-1.0) * sum2);
    f[1] = 1 - exp2;

    solution.objectives()[0] = f[0];
    solution.objectives()[1] = f[1];

    return solution;
  }
}


And this is how it is implemented in Julia:
function fonseca()
  problem = ContinuousProblem{Float64}("Fonseca")

  numberOfVariables = 3
  for _ in 1:numberOfVariables
    addVariable(problem, Bounds{Float64}(-4.0, 4.0))
  end

  f1 = x -> begin
    sum1 = 0.0
    for i in range(1, numberOfVariables)
      sum1 += ^(x[i] - (1.0 / sqrt(1.0 * numberOfVariables)), 2.0)
    end
    return 1.0 - exp(-1.0 * sum1)
  end

  f2 = x -> begin
    sum2 = 0.0
    for i in range(1, numberOfVariables)
      sum2 += ^(x[i] + (1.0 / sqrt(1.0 * numberOfVariables)), 2.0)
    end

    return 1.0 - exp(-1.0 * sum2)
  end

  addObjective(problem, f1)
  addObjective(problem, f2)

  return problem
end

Translate the Liao2008 problem to Julia, which implementation in Java is this:
package org.uma.jmetal.problem.multiobjective.rwa;


import java.util.ArrayList;
import java.util.List;
import org.uma.jmetal.problem.doubleproblem.impl.AbstractDoubleProblem;
import org.uma.jmetal.solution.doublesolution.DoubleSolution;

/**
 * Problem Liao2008 (RWA2) described in the paper "Engineering applications of
 * multi-objective evolutionary algorithms: A test suite of box-constrained real-world
 * problems". DOI: https://doi.org/10.1016/j.engappai.2023.106192
 */
public class Liao2008 extends AbstractDoubleProblem {

  public Liao2008() {
    numberOfObjectives(3);
    name("Liao2008");

    int numberOfVariables = 5;
    List<Double> lowerLimit = new ArrayList<>(numberOfVariables);
    List<Double> upperLimit = new ArrayList<>(numberOfVariables);

    for (int i = 0; i < numberOfVariables; i++) {
      lowerLimit.add(1.0);
      upperLimit.add(3.0);
    }

    variableBounds(lowerLimit, upperLimit);
  }

  @Override
  public DoubleSolution evaluate(DoubleSolution solution) {
    double Mass;
    double Ain;
    double Intrusion;
    double t1 = solution.variables().get(0);
    double t2 = solution.variables().get(1);
    double t3 = solution.variables().get(2);
    double t4 = solution.variables().get(3);
    double t5 = solution.variables().get(4);
    Mass = 1640.2823 + 2.3573285 * t1 + 2.3220035 * t2 + 4.5688768 * t3
        + 7.7213633 * t4 + 4.4559504 * t5;
    Ain = 6.5856 + 1.15 * t1 - 1.0427 * t2 + 0.9738 * t3 + 0.8364 * t4
        - 0.3695 * t1 * t4 + 0.0861 * t1 * t5 + 0.3628 * t2 * t4
        - 0.1106 * t1 * t1 - 0.3437 * t3 * t3 + 0.1764 * t4 * t4;
    Intrusion = -0.0551 + 0.0181 * t1 + 0.1024 * t2 + 0.0421 * t3
        - 0.0073 * t1 * t2 + 0.024 * t2 * t3 - 0.0118 * t2 * t4
        - 0.0204 * t3 * t4 - 0.008 * t3 * t5 - 0.0241 * t2 * t2
        + 0.0109 * t4 * t4;

    solution.objectives()[0] = Mass; /* Minimization */
    solution.objectives()[1] = Ain; /* Minimization */
    solution.objectives()[2] = Intrusion; /* Minimization */

    return solution;
  }
}
```
## ChatGPT
```
function liao2008()
    problem = ContinuousProblem{Float64}("Liao2008")

    numberOfVariables = 5
    lowerLimit = [1.0 for _ in 1:numberOfVariables]
    upperLimit = [3.0 for _ in 1:numberOfVariables]

    for i in 1:numberOfVariables
        addVariable(problem, Bounds{Float64}(lowerLimit[i], upperLimit[i]))
    end

    f1 = x -> begin
        t1, t2, t3, t4, t5 = x
        Mass = 1640.2823 + 2.3573285 * t1 + 2.3220035 * t2 + 4.5688768 * t3 +
               7.7213633 * t4 + 4.4559504 * t5
        return Mass
    end

    f2 = x -> begin
        t1, t2, t3, t4, t5 = x
        Ain = 6.5856 + 1.15 * t1 - 1.0427 * t2 + 0.9738 * t3 + 0.8364 * t4 -
              0.3695 * t1 * t4 + 0.0861 * t1 * t5 + 0.3628 * t2 * t4 -
              0.1106 * t1 * t1 - 0.3437 * t3 * t3 + 0.1764 * t4 * t4
        return Ain
    end

    f3 = x -> begin
        t1, t2, t3, t4, t5 = x
        Intrusion = -0.0551 + 0.0181 * t1 + 0.1024 * t2 + 0.0421 * t3 -
                    0.0073 * t1 * t2 + 0.024 * t2 * t3 - 0.0118 * t2 * t4 -
                    0.0204 * t3 * t4 - 0.008 * t3 * t5 - 0.0241 * t2 * t2 +
                    0.0109 * t4 * t4
        return Intrusion
    end

    addObjective(problem, f1)
    addObjective(problem, f2)
    addObjective(problem, f3)

    return problem
end

```
## Gemini
```
function liao2008()
  problem = ContinuousProblem{Float64}("Liao2008")

  # Define number of variables and bounds
  n_vars = 5
  lower_bounds = [1.0, 1.0, 1.0, 1.0, 1.0]
  upper_bounds = [3.0, 3.0, 3.0, 3.0, 3.0]
  for i in 1:n_vars
    addVariable(problem, Bounds{Float64}(lower_bounds[i], upper_bounds[i]))
  end

  # Define objective functions
  f1 = x -> begin  # Mass (Minimization)
    t1, t2, t3, t4, t5 = x
    Mass = 1640.2823 + 2.3573285 * t1 + 2.3220035 * t2 + 4.5688768 * t3
         + 7.7213633 * t4 + 4.4559504 * t5
    return Mass
  end

  f2 = x -> begin  # Ain (Minimization)
    t1, t2, t3, t4, t5 = x
    Ain = 6.5856 + 1.15 * t1 - 1.0427 * t2 + 0.9738 * t3 + 0.8364 * t4
         - 0.3695 * t1 * t4 + 0.0861 * t1 * t5 + 0.3628 * t2 * t4
         - 0.1106 * t1 * t1 - 0.3437 * t3 * t3 + 0.1764 * t4 * t4
    return Ain
  end

  f3 = x -> begin  # Intrusion (Minimization)
    t1, t2, t3, t4, t5 = x
    Intrusion = -0.0551 + 0.0181 * t1 + 0.1024 * t2 + 0.0421 * t3
               - 0.0073 * t1 * t2 + 0.024 * t2 * t3 - 0.0118 * t2 * t4
               - 0.0204 * t3 * t4 - 0.008 * t3 * t5 - 0.0241 * t2 * t2
               + 0.0109 * t4 * t4
    return Intrusion
  end

  # Add objectives
  addObjective(problem, f1)
  addObjective(problem, f2)
  addObjective(problem, f3)

  return problem
end
```
# RWA3: Ganesan2013
## Petición
```
This is the Java implementation for the Fonseca problem:
package org.uma.jmetal.problem.multiobjective;

import java.util.ArrayList;
import java.util.List;
import org.uma.jmetal.problem.doubleproblem.impl.AbstractDoubleProblem;
import org.uma.jmetal.solution.doublesolution.DoubleSolution;

/**
 * Class representing problem Fonseca
 */
@SuppressWarnings("serial")
public class Fonseca extends AbstractDoubleProblem {

  /**
   * Constructor
   */
  public Fonseca() {
    int numberOfVariables = 3;
    numberOfObjectives(2);
    numberOfConstraints(0);
    name("Fonseca");

    List<Double> lowerLimit = new ArrayList<>(numberOfVariables);
    List<Double> upperLimit = new ArrayList<>(numberOfVariables);

    for (int i = 0; i < numberOfVariables; i++) {
      lowerLimit.add(-4.0);
      upperLimit.add(4.0);
    }

    variableBounds(lowerLimit, upperLimit);
  }

  /**
   * Evaluate() method
   */
  @Override
  public DoubleSolution evaluate(DoubleSolution solution) {
    int numberOfVariables = numberOfVariables();

    double[] f = new double[solution.objectives().length];
    double[] x = new double[numberOfVariables];

    for (int i = 0; i < numberOfVariables; i++) {
      x[i] = solution.variables().get(i);
    }

    double sum1 = 0.0;
    for (int i = 0; i < numberOfVariables; i++) {
      sum1 += StrictMath.pow(x[i] - (1.0 / StrictMath.sqrt(numberOfVariables)), 2.0);
    }
    double exp1 = StrictMath.exp((-1.0) * sum1);
    f[0] = 1 - exp1;

    double sum2 = 0.0;
    for (int i = 0; i < numberOfVariables; i++) {
      sum2 += StrictMath.pow(x[i] + (1.0 / StrictMath.sqrt(numberOfVariables)), 2.0);
    }
    double exp2 = StrictMath.exp((-1.0) * sum2);
    f[1] = 1 - exp2;

    solution.objectives()[0] = f[0];
    solution.objectives()[1] = f[1];

    return solution;
  }
}


And this is how it is implemented in Julia:
function fonseca()
  problem = ContinuousProblem{Float64}("Fonseca")

  numberOfVariables = 3
  for _ in 1:numberOfVariables
    addVariable(problem, Bounds{Float64}(-4.0, 4.0))
  end

  f1 = x -> begin
    sum1 = 0.0
    for i in range(1, numberOfVariables)
      sum1 += ^(x[i] - (1.0 / sqrt(1.0 * numberOfVariables)), 2.0)
    end
    return 1.0 - exp(-1.0 * sum1)
  end

  f2 = x -> begin
    sum2 = 0.0
    for i in range(1, numberOfVariables)
      sum2 += ^(x[i] + (1.0 / sqrt(1.0 * numberOfVariables)), 2.0)
    end

    return 1.0 - exp(-1.0 * sum2)
  end

  addObjective(problem, f1)
  addObjective(problem, f2)

  return problem
end

Translate the Ganesan2013 problem to Julia, which implementation in Java is this:
package org.uma.jmetal.problem.multiobjective.rwa;


import java.util.List;
import org.uma.jmetal.problem.doubleproblem.impl.AbstractDoubleProblem;
import org.uma.jmetal.solution.doublesolution.DoubleSolution;

/**
 * Problem Ganesan2013 (RWA3) described in the paper "Engineering applications of
 * multi-objective evolutionary algorithms: A test suite of box-constrained real-world
 * problems". DOI: https://doi.org/10.1016/j.engappai.2023.106192
 */

public class Ganesan2013 extends AbstractDoubleProblem {

  public Ganesan2013() {
    numberOfObjectives(3);
    name("Ganesan2013") ;

    List<Double> lowerLimit = List.of(0.25, 10000.0, 600.0) ;
    List<Double> upperLimit = List.of(0.55, 20000.0, 1100.0) ;

    variableBounds(lowerLimit, upperLimit);
  }

  @Override
  public DoubleSolution evaluate(DoubleSolution solution) {
    double O2CH4 = solution.variables().get(0);
    double GV = solution.variables().get(1);
    double T = solution.variables().get(2);

    double HC4_conversion, CO_selectivity, H2_CO_ratio;

    HC4_conversion = (-8.87e-6)
        * (86.74 + 14.6 * O2CH4 - 3.06 * GV + 18.82 * T + 3.14 * O2CH4 * GV
        - 6.91 * O2CH4 * O2CH4 - 13.31 * T * T);

    CO_selectivity = (-2.152e-9)
        * (39.46 + 5.98 * O2CH4 - 2.4 * GV + 13.06 * T + 2.5 * O2CH4 * GV
        + 1.64 * GV * T - 3.9 * O2CH4 * O2CH4 - 10.15 * T * T
        - 3.69 * GV * GV * O2CH4) + 45.7;

    H2_CO_ratio = (4.425e-10)
        * (1.29 - 0.45 * T - 0.112 * O2CH4 * GV - 0.142 * T * GV
        + 0.109 * O2CH4 * O2CH4 + 0.405 * T * T
        + 0.167 * T * T * GV) + 0.18;

    solution.objectives()[0] = -HC4_conversion; /* maximization */
    solution.objectives()[1] = -CO_selectivity; /* maximization */
    solution.objectives()[2] = H2_CO_ratio; /* minimization */

    return solution ;
  }
}
```
## ChatGPT
```
function ganesan2013()
    problem = ContinuousProblem{Float64}("Ganesan2013")

    # Define the number of variables
    lowerLimit = [0.25, 10000.0, 600.0]
    upperLimit = [0.55, 20000.0, 1100.0]
    numberOfVariables = length(lowerLimit)

    # Set variable bounds
    for i in 1:numberOfVariables
        addVariable(problem, Bounds{Float64}(lowerLimit[i], upperLimit[i]))
    end

    # Objective 1: HC4_conversion (to be maximized)
    f1 = x -> begin
        O2CH4, GV, T = x
        HC4_conversion = (-8.87e-6) * (86.74 + 14.6 * O2CH4 - 3.06 * GV + 18.82 * T +
                                       3.14 * O2CH4 * GV - 6.91 * O2CH4^2 - 13.31 * T^2)
        return -HC4_conversion # since it's maximized, we return the negative value
    end

    # Objective 2: CO_selectivity (to be maximized)
    f2 = x -> begin
        O2CH4, GV, T = x
        CO_selectivity = (-2.152e-9) * (39.46 + 5.98 * O2CH4 - 2.4 * GV + 13.06 * T +
                                        2.5 * O2CH4 * GV + 1.64 * GV * T - 3.9 * O2CH4^2 - 
                                        10.15 * T^2 - 3.69 * GV^2 * O2CH4) + 45.7
        return -CO_selectivity # also maximizing, so return negative
    end

    # Objective 3: H2_CO_ratio (to be minimized)
    f3 = x -> begin
        O2CH4, GV, T = x
        H2_CO_ratio = (4.425e-10) * (1.29 - 0.45 * T - 0.112 * O2CH4 * GV - 
                                      0.142 * T * GV + 0.109 * O2CH4^2 + 
                                      0.405 * T^2 + 0.167 * T^2 * GV) + 0.18
        return H2_CO_ratio # minimization
    end

    # Add objectives to the problem
    addObjective(problem, f1)
    addObjective(problem, f2)
    addObjective(problem, f3)

    return problem
end

```
## Gemini
```
function ganesan2013()
  problem = ContinuousProblem{Float64}("Ganesan2013")

  # Define number of variables and bounds
  n_vars = 3
  lower_bounds = [0.25, 10000.0, 600.0]
  upper_bounds = [0.55, 20000.0, 1100.0]
  for i in 1:n_vars
    addVariable(problem, Bounds{Float64}(lower_bounds[i], upper_bounds[i]))
  end

  # Define objective functions
  f1 = x -> begin  # HC4_conversion (Maximization)
    O2CH4, GV, T = x
    HC4_conversion = - (8.87e-6) * (86.74 + 14.6 * O2CH4 - 3.06 * GV + 18.82 * T
                                    + 3.14 * O2CH4 * GV - 6.91 * O2CH4 * O2CH4 - 13.31 * T * T)
    return -HC4_conversion
  end

  f2 = x -> begin  # CO_selectivity (Maximization)
    O2CH4, GV, T = x
    CO_selectivity = - (2.152e-9) * (39.46 + 5.98 * O2CH4 - 2.4 * GV + 13.06 * T
                                     + 2.5 * O2CH4 * GV + 1.64 * GV * T - 3.9 * O2CH4 * O2CH4
                                     - 10.15 * T * T - 3.69 * GV * GV * O2CH4) + 45.7
    return -CO_selectivity
  end

  f3 = x -> begin  # H2_CO_ratio (Minimization)
    O2CH4, GV, T = x
    H2_CO_ratio = (4.425e-10) * (1.29 - 0.45 * T - 0.112 * O2CH4 * GV - 0.142 * T * GV
                                 + 0.109 * O2CH4 * O2CH4 + 0.405 * T * T
                                 + 0.167 * T * T * GV) + 0.18
    return H2_CO_ratio
  end

  # Add objectives
  addObjective(problem, f1)
  addObjective(problem, f2)
  addObjective(problem, f3)

  return problem
end
```
# RWA4: Padhi2016
## Petición
```
This is the Java implementation for the Fonseca problem:
package org.uma.jmetal.problem.multiobjective;

import java.util.ArrayList;
import java.util.List;
import org.uma.jmetal.problem.doubleproblem.impl.AbstractDoubleProblem;
import org.uma.jmetal.solution.doublesolution.DoubleSolution;

/**
 * Class representing problem Fonseca
 */
@SuppressWarnings("serial")
public class Fonseca extends AbstractDoubleProblem {

  /**
   * Constructor
   */
  public Fonseca() {
    int numberOfVariables = 3;
    numberOfObjectives(2);
    numberOfConstraints(0);
    name("Fonseca");

    List<Double> lowerLimit = new ArrayList<>(numberOfVariables);
    List<Double> upperLimit = new ArrayList<>(numberOfVariables);

    for (int i = 0; i < numberOfVariables; i++) {
      lowerLimit.add(-4.0);
      upperLimit.add(4.0);
    }

    variableBounds(lowerLimit, upperLimit);
  }

  /**
   * Evaluate() method
   */
  @Override
  public DoubleSolution evaluate(DoubleSolution solution) {
    int numberOfVariables = numberOfVariables();

    double[] f = new double[solution.objectives().length];
    double[] x = new double[numberOfVariables];

    for (int i = 0; i < numberOfVariables; i++) {
      x[i] = solution.variables().get(i);
    }

    double sum1 = 0.0;
    for (int i = 0; i < numberOfVariables; i++) {
      sum1 += StrictMath.pow(x[i] - (1.0 / StrictMath.sqrt(numberOfVariables)), 2.0);
    }
    double exp1 = StrictMath.exp((-1.0) * sum1);
    f[0] = 1 - exp1;

    double sum2 = 0.0;
    for (int i = 0; i < numberOfVariables; i++) {
      sum2 += StrictMath.pow(x[i] + (1.0 / StrictMath.sqrt(numberOfVariables)), 2.0);
    }
    double exp2 = StrictMath.exp((-1.0) * sum2);
    f[1] = 1 - exp2;

    solution.objectives()[0] = f[0];
    solution.objectives()[1] = f[1];

    return solution;
  }
}


And this is how it is implemented in Julia:
function fonseca()
  problem = ContinuousProblem{Float64}("Fonseca")

  numberOfVariables = 3
  for _ in 1:numberOfVariables
    addVariable(problem, Bounds{Float64}(-4.0, 4.0))
  end

  f1 = x -> begin
    sum1 = 0.0
    for i in range(1, numberOfVariables)
      sum1 += ^(x[i] - (1.0 / sqrt(1.0 * numberOfVariables)), 2.0)
    end
    return 1.0 - exp(-1.0 * sum1)
  end

  f2 = x -> begin
    sum2 = 0.0
    for i in range(1, numberOfVariables)
      sum2 += ^(x[i] + (1.0 / sqrt(1.0 * numberOfVariables)), 2.0)
    end

    return 1.0 - exp(-1.0 * sum2)
  end

  addObjective(problem, f1)
  addObjective(problem, f2)

  return problem
end

Translate the Padhi2016 problem to Julia, which implementation in Java is this:
package org.uma.jmetal.problem.multiobjective.rwa;


import java.util.List;
import org.uma.jmetal.problem.doubleproblem.impl.AbstractDoubleProblem;
import org.uma.jmetal.solution.doublesolution.DoubleSolution;

/**
 * Problem Padhi2016 (RWA4) described in the paper "Engineering applications of
 * multi-objective evolutionary algorithms: A test suite of box-constrained real-world
 * problems". DOI: https://doi.org/10.1016/j.engappai.2023.106192
 */
public class Padhi2016 extends AbstractDoubleProblem {

  public Padhi2016() {
    numberOfObjectives(3);
    name("Padhi2016");

    List<Double> lowerLimit = List.of(1.0, 10.0, 850.0, 20.0, 4.0) ;
    List<Double> upperLimit = List.of(1.4, 26.0, 1650.0, 40.0, 8.0) ;

    variableBounds(lowerLimit, upperLimit);
  }

  @Override
  public DoubleSolution evaluate(DoubleSolution solution) {
    double x1 = solution.variables().get(0);
    double x2 = solution.variables().get(1);
    double x3 = solution.variables().get(2);
    double x4 = solution.variables().get(3);
    double x5 = solution.variables().get(4);
    double CR, Ra, DD;

    CR = 1.74 + 0.42 * x1 - 0.27 * x2 + 0.087 * x3 - 0.19 * x4 + 0.18 * x5
        + 0.11 * x1 * x1 + 0.036 * x4 * x4 - 0.025 * x5 * x5
        + 0.044 * x1 * x2 + 0.034 * x1 * x4 + 0.17 * x1 * x5
        - 0.028 * x2 * x4 + 0.093 * x3 * x4 - 0.033 * x4 * x5;

    Ra = 2.19 + 0.26 * x1 - 0.088 * x2 + 0.037 * x3 - 0.16 * x4 + 0.069 * x5
        + 0.036 * x1 * x1 + 0.11 * x1 * x3 - 0.077 * x1 * x4
        - 0.075 * x2 * x3 + 0.054 * x2 * x4 + 0.090 * x3 * x5
        + 0.041 * x4 * x5;

    DD = 0.095 + 0.013 * x1 - 8.625 * 1e-003 * x2 - 5.458 * 1e-003 * x3
        - 0.012 * x4 + 1.462 * 1e-003 * x1 * x1 - 6.635 * 1e-004 * x2 * x2
        - 1.788 * 1e-003 * x4 * x4 - 0.011 * x1 * x2
        - 6.188 * 1e-003 * x1 * x3 + 8.937 * 1e-003 * x1 * x4
        - 4.563 * 1e-003 * x1 * x5 - 0.012 * x2 * x3
        - 1.063 * 1e-003 * x2 * x4 + 2.438 * 1e-003 * x2 * x5
        - 1.937 * 1e-003 * x3 * x4 - 1.188 * 1e-003 * x3 * x5
        - 3.312 * 1e-003 * x4 * x5;

    solution.objectives()[0] = -CR; /* Maximization */
    solution.objectives()[1] = Ra; /* Minimization */
    solution.objectives()[2] = DD; /* Minimization */

    return solution ;
  }
}
```
## ChatGPT
```
function padhi2016()
    problem = ContinuousProblem{Float64}("Padhi2016")

    # Define variable bounds
    lowerLimit = [1.0, 10.0, 850.0, 20.0, 4.0]
    upperLimit = [1.4, 26.0, 1650.0, 40.0, 8.0]
    numberOfVariables = length(lowerLimit)

    # Set variable bounds
    for i in 1:numberOfVariables
        addVariable(problem, Bounds{Float64}(lowerLimit[i], upperLimit[i]))
    end

    # Objective 1: CR (to be maximized)
    f1 = x -> begin
        x1, x2, x3, x4, x5 = x
        CR = 1.74 + 0.42 * x1 - 0.27 * x2 + 0.087 * x3 - 0.19 * x4 + 0.18 * x5 +
            0.11 * x1^2 + 0.036 * x4^2 - 0.025 * x5^2 +
            0.044 * x1 * x2 + 0.034 * x1 * x4 + 0.17 * x1 * x5 -
            0.028 * x2 * x4 + 0.093 * x3 * x4 - 0.033 * x4 * x5
        return -CR # Maximization, so return negative
    end

    # Objective 2: Ra (to be minimized)
    f2 = x -> begin
        x1, x2, x3, x4, x5 = x
        Ra = 2.19 + 0.26 * x1 - 0.088 * x2 + 0.037 * x3 - 0.16 * x4 + 0.069 * x5 +
            0.036 * x1^2 + 0.11 * x1 * x3 - 0.077 * x1 * x4 -
            0.075 * x2 * x3 + 0.054 * x2 * x4 + 0.09 * x3 * x5 +
            0.041 * x4 * x5
        return Ra # Minimization
    end

    # Objective 3: DD (to be minimized)
    f3 = x -> begin
        x1, x2, x3, x4, x5 = x
        DD = 0.095 + 0.013 * x1 - 8.625e-3 * x2 - 5.458e-3 * x3 -
            0.012 * x4 + 1.462e-3 * x1^2 - 6.635e-4 * x2^2 -
            1.788e-3 * x4^2 - 0.011 * x1 * x2 -
            6.188e-3 * x1 * x3 + 8.937e-3 * x1 * x4 -
            4.563e-3 * x1 * x5 - 0.012 * x2 * x3 -
            1.063e-3 * x2 * x4 + 2.438e-3 * x2 * x5 -
            1.937e-3 * x3 * x4 - 1.188e-3 * x3 * x5 -
            3.312e-3 * x4 * x5
        return DD # Minimization
    end

    # Add objectives to the problem
    addObjective(problem, f1)
    addObjective(problem, f2)
    addObjective(problem, f3)

    return problem
end

```
## Gemini
```
function padhi2016()
  problem = ContinuousProblem{Float64}("Padhi2016")

  # Define number of variables and bounds
  n_vars = 5
  lower_bounds = [1.0, 10.0, 850.0, 20.0, 4.0]
  upper_bounds = [1.4, 26.0, 1650.0, 40.0, 8.0]
  for i in 1:n_vars
    addVariable(problem, Bounds{Float64}(lower_bounds[i], upper_bounds[i]))
  end

  # Define objective functions
  f1 = x -> begin  # CR (Maximization)
    x1, x2, x3, x4, x5 = x
    CR = 1.74 + 0.42 * x1 - 0.27 * x2 + 0.087 * x3 - 0.19 * x4 + 0.18 * x5
         + 0.11 * x1 * x1 + 0.036 * x4 * x4 - 0.025 * x5 * x5
         + 0.044 * x1 * x2 + 0.034 * x1 * x4 + 0.17 * x1 * x5
         - 0.028 * x2 * x4 + 0.093 * x3 * x4 - 0.033 * x4 * x5
    return -CR  # Convert to minimization for consistency
  end

  f2 = x -> begin  # Ra (Minimization)
    x1, x2, x3, x4, x5 = x
    Ra = 2.19 + 0.26 * x1 - 0.088 * x2 + 0.037 * x3 - 0.16 * x4 + 0.069 * x5
         + 0.036 * x1 * x1 + 0.11 * x1 * x3 - 0.077 * x1 * x4
         - 0.075 * x2 * x3 + 0.054 * x2 * x4 + 0.090 * x3 * x5
         + 0.041 * x4 * x5
    return Ra
  end

  f3 = x -> begin  # DD (Minimization)
    x1, x2, x3, x4, x5 = x
    DD = 0.095 + 0.013 * x1 - 8.625e-3 * x2 - 5.458e-3 * x3
         - 0.012 * x4 + 1.462e-3 * x1 * x1 - 6.635e-4 * x2 * x2
         - 1.788e-3 * x4 * x4 - 0.011 * x1 * x2 - 6.188e-3 * x1 * x3
         + 8.937e-3 * x1 * x4 - 4.563e-3 * x1 * x5 - 0.012 * x2 * x3
         - 1.063e-3 * x2 * x4 + 2.438e-3 * x2 * x5 - 1.937e-3 * x3 * x4
         - 1.188e-3 * x3 * x5 - 3.312e-3 * x4 * x5
    return DD
  end

  # Add objectives
  addObjective(problem, f1)
  addObjective(problem, f2)
  addObjective(problem, f3)

  return problem
end
```
# RWA5: Gao2020
## Petición
```
This is the Java implementation for the Fonseca problem:
package org.uma.jmetal.problem.multiobjective;

import java.util.ArrayList;
import java.util.List;
import org.uma.jmetal.problem.doubleproblem.impl.AbstractDoubleProblem;
import org.uma.jmetal.solution.doublesolution.DoubleSolution;

/**
 * Class representing problem Fonseca
 */
@SuppressWarnings("serial")
public class Fonseca extends AbstractDoubleProblem {

  /**
   * Constructor
   */
  public Fonseca() {
    int numberOfVariables = 3;
    numberOfObjectives(2);
    numberOfConstraints(0);
    name("Fonseca");

    List<Double> lowerLimit = new ArrayList<>(numberOfVariables);
    List<Double> upperLimit = new ArrayList<>(numberOfVariables);

    for (int i = 0; i < numberOfVariables; i++) {
      lowerLimit.add(-4.0);
      upperLimit.add(4.0);
    }

    variableBounds(lowerLimit, upperLimit);
  }

  /**
   * Evaluate() method
   */
  @Override
  public DoubleSolution evaluate(DoubleSolution solution) {
    int numberOfVariables = numberOfVariables();

    double[] f = new double[solution.objectives().length];
    double[] x = new double[numberOfVariables];

    for (int i = 0; i < numberOfVariables; i++) {
      x[i] = solution.variables().get(i);
    }

    double sum1 = 0.0;
    for (int i = 0; i < numberOfVariables; i++) {
      sum1 += StrictMath.pow(x[i] - (1.0 / StrictMath.sqrt(numberOfVariables)), 2.0);
    }
    double exp1 = StrictMath.exp((-1.0) * sum1);
    f[0] = 1 - exp1;

    double sum2 = 0.0;
    for (int i = 0; i < numberOfVariables; i++) {
      sum2 += StrictMath.pow(x[i] + (1.0 / StrictMath.sqrt(numberOfVariables)), 2.0);
    }
    double exp2 = StrictMath.exp((-1.0) * sum2);
    f[1] = 1 - exp2;

    solution.objectives()[0] = f[0];
    solution.objectives()[1] = f[1];

    return solution;
  }
}


And this is how it is implemented in Julia:
function fonseca()
  problem = ContinuousProblem{Float64}("Fonseca")

  numberOfVariables = 3
  for _ in 1:numberOfVariables
    addVariable(problem, Bounds{Float64}(-4.0, 4.0))
  end

  f1 = x -> begin
    sum1 = 0.0
    for i in range(1, numberOfVariables)
      sum1 += ^(x[i] - (1.0 / sqrt(1.0 * numberOfVariables)), 2.0)
    end
    return 1.0 - exp(-1.0 * sum1)
  end

  f2 = x -> begin
    sum2 = 0.0
    for i in range(1, numberOfVariables)
      sum2 += ^(x[i] + (1.0 / sqrt(1.0 * numberOfVariables)), 2.0)
    end

    return 1.0 - exp(-1.0 * sum2)
  end

  addObjective(problem, f1)
  addObjective(problem, f2)

  return problem
end

Translate the Gao2020 problem to Julia, which implementation in Java is this:
package org.uma.jmetal.problem.multiobjective.rwa;

import java.util.List;
import org.uma.jmetal.problem.doubleproblem.impl.AbstractDoubleProblem;
import org.uma.jmetal.solution.doublesolution.DoubleSolution;

/**
 * Problem Gao2020 (RWA5) described in the paper "Engineering applications of
 * multi-objective evolutionary algorithms: A test suite of box-constrained real-world
 * problems". DOI: https://doi.org/10.1016/j.engappai.2023.106192
 */
public class Gao2020 extends AbstractDoubleProblem {

  public Gao2020() {
    numberOfObjectives(3);
    name("Gao2020");

    List<Double> lowerLimit = List.of(40.0, 0.35, 333.0, 20.0, 3000.0, 0.1, 308.0, 150.0, 0.1);
    List<Double> upperLimit = List.of(100.0, 0.5, 363.0, 40.0, 4000.0, 3.0, 328.0, 200.0, 2.0);

    variableBounds(lowerLimit, upperLimit);
  }

  @Override
  public DoubleSolution evaluate(DoubleSolution solution) {
    double A = solution.variables().get(0);
    double B = solution.variables().get(1);
    double C = solution.variables().get(2);
    double D = solution.variables().get(3);
    double E = solution.variables().get(4);
    double F = solution.variables().get(5);
    double G = solution.variables().get(6);
    double H = solution.variables().get(7);
    double J = solution.variables().get(8);

    double t_eff = 171.33 + 23.25 * A - 8.61 * B - 59.85 * C - 66.12 * D - 15.29 * E
        - 83.32 * F + 37.72 * G + 12.67 * H + 0.46 * J - 0.47 * A * B
        - 0.30 * A * C - 6.22 * A * D - 0.62 * A * E - 42.48 * A * F
        + 3.11 * A * G + 4.45 * A * H - 0.22 * A * J + 7.46 * B * C
        + 3.28 * B * D + 1.28 * B * E + 1.02 * B * F - 4.02 * B * G
        - 2.29 * B * H - 0.16 * B * J + 19.25 * C * D - 14.83 * C * E
        + 5.07 * C * F - 37.61 * C * G - 9.11 * C * H - 0.32 * C * J
        + 8.53 * D * E + 18.46 * D * F - 14.28 * D * G - 7.05 * D * H
        - 0.24 * D * J + 2.05 * E * F + 15.73 * E * G - 0.77 * E * H
        - 0.29 * E * J - 4.77 * F * G + 2.07 * F * H + 0.64 * F * J
        + 3.41 * G * H + 1.76 * G * J + 0.48 * H * J + 3.64 * A * A
        - 0.99 * B * B + 30.5 * C * C + 21.63 * D * D + 1.72 * E * E
        + 72.42 * F * F + 11.2 * G * G + 1.86 * H * H - 0.79 * J * J;

    double Q_eff = 577.73 - 1.22 * A - 19.56 * B + 102.05 * C - 1.83 * D + 27.28 * E
        + 2.52 * F + 5.43 * G + 37.48 * H + 0.45 * J + 2.94 * A * B
        - 2.96 * A * C + 0.66 * A * D + 0.09 * A * E - 0.43 * A * F
        + 0.12 * A * G - 0.43 * A * H - 0.7 * A * J + 8.05 * B * C
        + 0.53 * B * D + 4.43 * B * E - 0.6 * B * F - 0.46 * B * G
        - 4.97 * B * H + 0.046 * B * J + 0.42 * C * D + 6.03 * C * E
        + 0.21 * C * F + 2.63 * C * G + 0.17 * C * H - 0.43 * C * J
        + 6.34 * D * E + 6.36 * D * F + 0.19 * D * G - 0.22 * D * H
        + 0.39 * D * J - 7.09 * E * F + 3.06 * E * G - 0.15 * E * H
        + 0.68 * E * J - 0.2 * F * G + 0.14 * F * H + 0.88 * F * J
        + 0.45 * G * H - 0.014 * G * J + 0.99 * H * J + 0.55 * A * A
        - 4.97 * B * B - 0.47 * C * C - 0.91 * D * D - 2.08 * E * E
        - 1.43 * F * F + 0.43 * G * G + 1.06 * H * H + 0.98 * J * J;

    double Phi_ex = 0.81 - 9.26e-3 * A + 0.014 * B - 0.029 * C - 7.69e-4 * D
        + 4.05e-3 * E + 0.029 * F + 0.075 * G - 0.012 * H
        - 1.04e-3 * J - 2.63e-3 * A * B + 1.34e-4 * A * C
        - 1.48e-3 * A * D - 7.04e-4 * A * E + 0.013 * A * F
        + 6.55e-4 * A * G - 9.71e-3 * A * H + 1.08e-3 * A * J
        + 2.54e-3 * B * C - 4.83e-4 * B * D + 9.63e-4 * B * E
        + 1.21e-3 * B * F - 7.02e-3 * B * G - 1.21e-3 * B * H
        + 1.94e-5 * B * J - 1.15e-3 * C * D + 3.60e-3 * C * E
        + 5.60e-3 * C * F - 0.026 * C * G - 4.01e-3 * C * H
        + 1.35e-3 * C * J - 6.93e-3 * D * E - 3.16e-3 * D * F
        - 2.38e-4 * D * G + 7.32e-4 * D * H + 4.69e-4 * D * J
        + 8.18e-3 * E * F - 5.74e-3 * E * G + 1.44e-4 * E * H
        - 9.95e-5 * E * J - 2.09e-3 * F * G - 65e-4 * F * H
        - 1.99e-3 * F * J + 4.95e-3 * G * H + 8.70e-4 * G * J
        + 4.55e-4 * H * J - 9.32e-4 * A * A - 7.61e-4 * B * B
        + 0.016 * C * C + 1.24e-3 * D * D + 9.61e-4 * E * E
        - 0.024 * F * F - 8.63e-3 * G * G - 1.90e-4 * H * H
        - 7.56e-4 * J * J;

    solution.objectives()[0] = t_eff; /* Minimize */
    solution.objectives()[1] = -Q_eff; /* Maximize */
    solution.objectives()[2] = -Phi_ex; /* Maximize */

    return solution ;
  }
}
```
## ChatGPT
```
function gao2020()
  problem = ContinuousProblem{Float64}("Gao2020")

  # Define variable bounds
  lower_limits = [40.0, 0.35, 333.0, 20.0, 3000.0, 0.1, 308.0, 150.0, 0.1]
  upper_limits = [100.0, 0.5, 363.0, 40.0, 4000.0, 3.0, 328.0, 200.0, 2.0]

  for i in 1:length(lower_limits)
    addVariable(problem, Bounds{Float64}(lower_limits[i], upper_limits[i]))
  end

  # Define the first objective (t_eff)
  f1 = x -> begin
    A, B, C, D, E, F, G, H, J = x
    t_eff = 171.33 + 23.25 * A - 8.61 * B - 59.85 * C - 66.12 * D - 15.29 * E - 
            83.32 * F + 37.72 * G + 12.67 * H + 0.46 * J - 0.47 * A * B - 
            0.30 * A * C - 6.22 * A * D - 0.62 * A * E - 42.48 * A * F + 
            3.11 * A * G + 4.45 * A * H - 0.22 * A * J + 7.46 * B * C + 
            3.28 * B * D + 1.28 * B * E + 1.02 * B * F - 4.02 * B * G - 
            2.29 * B * H - 0.16 * B * J + 19.25 * C * D - 14.83 * C * E + 
            5.07 * C * F - 37.61 * C * G - 9.11 * C * H - 0.32 * C * J + 
            8.53 * D * E + 18.46 * D * F - 14.28 * D * G - 7.05 * D * H - 
            0.24 * D * J + 2.05 * E * F + 15.73 * E * G - 0.77 * E * H - 
            0.29 * E * J - 4.77 * F * G + 2.07 * F * H + 0.64 * F * J + 
            3.41 * G * H + 1.76 * G * J + 0.48 * H * J + 3.64 * A^2 - 
            0.99 * B^2 + 30.5 * C^2 + 21.63 * D^2 + 1.72 * E^2 + 72.42 * F^2 + 
            11.2 * G^2 + 1.86 * H^2 - 0.79 * J^2
    return t_eff
  end

  # Define the second objective (Q_eff)
  f2 = x -> begin
    A, B, C, D, E, F, G, H, J = x
    Q_eff = 577.73 - 1.22 * A - 19.56 * B + 102.05 * C - 1.83 * D + 27.28 * E + 
            2.52 * F + 5.43 * G + 37.48 * H + 0.45 * J + 2.94 * A * B - 
            2.96 * A * C + 0.66 * A * D + 0.09 * A * E - 0.43 * A * F + 
            0.12 * A * G - 0.43 * A * H - 0.7 * A * J + 8.05 * B * C + 
            0.53 * B * D + 4.43 * B * E - 0.6 * B * F - 0.46 * B * G - 
            4.97 * B * H + 0.046 * B * J + 0.42 * C * D + 6.03 * C * E + 
            0.21 * C * F + 2.63 * C * G + 0.17 * C * H - 0.43 * C * J + 
            6.34 * D * E + 6.36 * D * F + 0.19 * D * G - 0.22 * D * H + 
            0.39 * D * J - 7.09 * E * F + 3.06 * E * G - 0.15 * E * H + 
            0.68 * E * J - 0.2 * F * G + 0.14 * F * H + 0.88 * F * J + 
            0.45 * G * H - 0.014 * G * J + 0.99 * H * J + 0.55 * A^2 - 
            4.97 * B^2 - 0.47 * C^2 - 0.91 * D^2 - 2.08 * E^2 - 1.43 * F^2 + 
            0.43 * G^2 + 1.06 * H^2 + 0.98 * J^2
    return -Q_eff  # Negated because it is a maximization objective
  end

  # Define the third objective (Phi_ex)
  f3 = x -> begin
    A, B, C, D, E, F, G, H, J = x
    Phi_ex = 0.81 - 9.26e-3 * A + 0.014 * B - 0.029 * C - 7.69e-4 * D + 
             4.05e-3 * E + 0.029 * F + 0.075 * G - 0.012 * H - 1.04e-3 * J - 
             2.63e-3 * A * B + 1.34e-4 * A * C - 1.48e-3 * A * D - 
             7.04e-4 * A * E + 0.013 * A * F + 6.55e-4 * A * G - 
             9.71e-3 * A * H + 1.08e-3 * A * J + 2.54e-3 * B * C - 
             4.83e-4 * B * D + 9.63e-4 * B * E + 1.21e-3 * B * F - 
             7.02e-3 * B * G - 1.21e-3 * B * H + 1.94e-5 * B * J - 
             1.15e-3 * C * D + 3.60e-3 * C * E + 5.60e-3 * C * F - 
             0.026 * C * G - 4.01e-3 * C * H + 1.35e-3 * C * J - 
             6.93e-3 * D * E - 3.16e-3 * D * F - 2.38e-4 * D * G + 
             7.32e-4 * D * H + 4.69e-4 * D * J + 8.18e-3 * E * F - 
             5.74e-3 * E * G + 1.44e-4 * E * H - 9.95e-5 * E * J - 
             2.09e-3 * F * G - 65e-4 * F * H - 1.99e-3 * F * J + 
             4.95e-3 * G * H + 8.70e-4 * G * J + 4.55e-4 * H * J - 
             9.32e-4 * A^2 - 7.61e-4 * B^2 + 0.016 * C^2 + 1.24e-3 * D^2 + 
             9.61e-4 * E^2 - 0.024 * F^2 - 8.63e-3 * G^2 - 1.90e-4 * H^2 - 
             7.56e-4 * J^2
    return -Phi_ex  # Negated because it is a maximization objective
  end

  # Add objectives to the problem
  addObjective(problem, f1)
  addObjective(problem, f2)
  addObjective(problem, f3)

  return problem
end

```
## Gemini
```
function gao2020()
  problem = ContinuousProblem{Float64}("Gao2020")

  # Define number of variables and bounds
  n_vars = 9
  lower_bounds = [40.0, 0.35, 333.0, 20.0, 3000.0, 0.1, 308.0, 150.0, 0.1]
  upper_bounds = [100.0, 0.5, 363.0, 40.0, 4000.0, 3.0, 328.0, 200.0, 2.0]
  for i in 1:n_vars
    addVariable(problem, Bounds{Float64}(lower_bounds[i], upper_bounds[i]))
  end

  # Define objective functions
  f1 = x -> begin  # t_eff (Minimization)
    A, B, C, D, E, F, G, H, J = x
    t_eff = 171.33 + 23.25 * A - 8.61 * B - 59.85 * C - 66.12 * D - 15.29 * E
          - 83.32 * F + 37.72 * G + 12.67 * H + 0.46 * J - 0.47 * A * B
          - 0.30 * A * C - 6.22 * A * D - 0.62 * A * E - 42.48 * A * F
          + 3.11 * A * G + 4.45 * A * H - 0.22 * A * J + 7.46 * B * C
          + 3.28 * B * D + 1.28 * B * E + 1.02 * B * F - 4.02 * B * G
          - 2.29 * B * H - 0.16 * B * J + 19.25 * C * D - 14.83 * C * E
          + 5.07 * C * F - 37.61 * C * G - 9.11 * C * H - 0.32 * C * J
          + 8.53 * D * E + 18.46 * D * F - 14.28 * D * G - 7.05 * D * H
          - 0.24 * D * J + 2.05 * E * F + 15.73 * E * G - 0.77 * E * H
          - 0.29 * E * J - 4.77 * F * G + 2.07 * F * H + 0.64 * F * J
          + 3.41 * G * H + 1.76 * G * J + 0.48 * H * J + 3.64 * A * A
          - 0.99 * B * B + 30.5 * C * C + 21.63 * D * D + 1.72 * E * E
          + 72.42 * F * F + 11.2 * G * G + 1.86 * H * H - 0.79 * J * J
    return t_eff
  end

  f2 = x -> begin  # Q_eff (Maximization)
    A, B, C, D, E, F, G, H, J = x
    Q_eff = 577.73 - 1.22 * A - 19.56 * B + 102.05 * C - 1.83 * D + 27.28 * E
          + 2.52 * F + 5.43 * G + 37.48 * H + 0.45 * J + 2.94 * A * B
          - 2.96 * A * C + 0.66 * A * D + 0.09
```
# RWA6: Xu2020
## Petición
```
This is the Java implementation for the Fonseca problem:
package org.uma.jmetal.problem.multiobjective;

import java.util.ArrayList;
import java.util.List;
import org.uma.jmetal.problem.doubleproblem.impl.AbstractDoubleProblem;
import org.uma.jmetal.solution.doublesolution.DoubleSolution;

/**
 * Class representing problem Fonseca
 */
@SuppressWarnings("serial")
public class Fonseca extends AbstractDoubleProblem {

  /**
   * Constructor
   */
  public Fonseca() {
    int numberOfVariables = 3;
    numberOfObjectives(2);
    numberOfConstraints(0);
    name("Fonseca");

    List<Double> lowerLimit = new ArrayList<>(numberOfVariables);
    List<Double> upperLimit = new ArrayList<>(numberOfVariables);

    for (int i = 0; i < numberOfVariables; i++) {
      lowerLimit.add(-4.0);
      upperLimit.add(4.0);
    }

    variableBounds(lowerLimit, upperLimit);
  }

  /**
   * Evaluate() method
   */
  @Override
  public DoubleSolution evaluate(DoubleSolution solution) {
    int numberOfVariables = numberOfVariables();

    double[] f = new double[solution.objectives().length];
    double[] x = new double[numberOfVariables];

    for (int i = 0; i < numberOfVariables; i++) {
      x[i] = solution.variables().get(i);
    }

    double sum1 = 0.0;
    for (int i = 0; i < numberOfVariables; i++) {
      sum1 += StrictMath.pow(x[i] - (1.0 / StrictMath.sqrt(numberOfVariables)), 2.0);
    }
    double exp1 = StrictMath.exp((-1.0) * sum1);
    f[0] = 1 - exp1;

    double sum2 = 0.0;
    for (int i = 0; i < numberOfVariables; i++) {
      sum2 += StrictMath.pow(x[i] + (1.0 / StrictMath.sqrt(numberOfVariables)), 2.0);
    }
    double exp2 = StrictMath.exp((-1.0) * sum2);
    f[1] = 1 - exp2;

    solution.objectives()[0] = f[0];
    solution.objectives()[1] = f[1];

    return solution;
  }
}


And this is how it is implemented in Julia:
function fonseca()
  problem = ContinuousProblem{Float64}("Fonseca")

  numberOfVariables = 3
  for _ in 1:numberOfVariables
    addVariable(problem, Bounds{Float64}(-4.0, 4.0))
  end

  f1 = x -> begin
    sum1 = 0.0
    for i in range(1, numberOfVariables)
      sum1 += ^(x[i] - (1.0 / sqrt(1.0 * numberOfVariables)), 2.0)
    end
    return 1.0 - exp(-1.0 * sum1)
  end

  f2 = x -> begin
    sum2 = 0.0
    for i in range(1, numberOfVariables)
      sum2 += ^(x[i] + (1.0 / sqrt(1.0 * numberOfVariables)), 2.0)
    end

    return 1.0 - exp(-1.0 * sum2)
  end

  addObjective(problem, f1)
  addObjective(problem, f2)

  return problem
end

Translate the Xu2020 problem to Julia, which implementation in Java is this:
package org.uma.jmetal.problem.multiobjective.rwa;


import java.util.List;
import org.uma.jmetal.problem.doubleproblem.impl.AbstractDoubleProblem;
import org.uma.jmetal.solution.doublesolution.DoubleSolution;

/**
 * Problem Xu2020 (RWA6) described in the paper "Engineering applications of
 * multi-objective evolutionary algorithms: A test suite of box-constrained real-world
 * problems". DOI: https://doi.org/10.1016/j.engappai.2023.106192
 */
public class Xu2020 extends AbstractDoubleProblem {

  public Xu2020() {
    numberOfObjectives(3);
    name("Xu2020");

    List<Double> lowerLimit = List.of(12.56, 0.02, 1.0, 0.5);
    List<Double> upperLimit = List.of(25.12, 0.06, 5.0, 2.0);

    variableBounds(lowerLimit, upperLimit);
  }

  @Override
  public DoubleSolution evaluate(DoubleSolution solution) {
    double vc = solution.variables().get(0);
    double fz = solution.variables().get(1);
    double ap = solution.variables().get(2);
    double ae = solution.variables().get(3);

    double Ft, Ra, MRR;
    double d = 2.5;
    double z = 1.0;

    Ft = -54.3 - 1.18 * vc - 2429 * fz + 104.2 * ap + 129.0 * ae
        - 18.9 * vc * fz - 0.209 * vc * ap - 0.673 * vc * ae + 265 * fz * ap
        + 1209 * fz * ae + 22.76 * ap * ae + 0.066 * vc * vc
        + 32117 * fz * fz - 16.98 * ap * ap - 47.6 * ae * ae;

    Ra = 0.227 - 0.0072 * vc + 1.89 * fz - 0.0203 * ap + 0.3075 * ae
        - 0.198 * vc * fz - 0.000955 * vc * ap - 0.00656 * vc * ae
        + 0.209 * fz * ap + 0.783 * fz * ae + 0.02275 * ap * ae
        + 0.000355 * vc * vc + 35 * fz * fz + 0.00037 * ap * ap
        - 0.0791 * ae * ae;

    MRR = (1000.0 * vc * fz * z * ap * ae) / (Math.PI * d);

    solution.objectives()[0] = Ft; // Minimization
    solution.objectives()[1] = Ra; // Minimization
    solution.objectives()[2] = -MRR; // Maximization

    return solution ;
  }
}
```
## ChatGPT
```
function xu2020()
    problem = ContinuousProblem{Float64}("Xu2020")

    # Define variable bounds
    lower_limits = [12.56, 0.02, 1.0, 0.5]
    upper_limits = [25.12, 0.06, 5.0, 2.0]

    for i in 1:length(lower_limits)
        addVariable(problem, Bounds{Float64}(lower_limits[i], upper_limits[i]))
    end

    # Constants
    d = 2.5
    z = 1.0

    # Define the first objective (Ft)
    f1 = x -> begin
        vc, fz, ap, ae = x
        Ft = -54.3 - 1.18 * vc - 2429 * fz + 104.2 * ap + 129.0 * ae - 
             18.9 * vc * fz - 0.209 * vc * ap - 0.673 * vc * ae + 
             265 * fz * ap + 1209 * fz * ae + 22.76 * ap * ae + 
             0.066 * vc^2 + 32117 * fz^2 - 16.98 * ap^2 - 47.6 * ae^2
        return Ft
    end

    # Define the second objective (Ra)
    f2 = x -> begin
        vc, fz, ap, ae = x
        Ra = 0.227 - 0.0072 * vc + 1.89 * fz - 0.0203 * ap + 0.3075 * ae - 
             0.198 * vc * fz - 0.000955 * vc * ap - 0.00656 * vc * ae + 
             0.209 * fz * ap + 0.783 * fz * ae + 0.02275 * ap * ae + 
             0.000355 * vc^2 + 35 * fz^2 + 0.00037 * ap^2 - 0.0791 * ae^2
        return Ra
    end

    # Define the third objective (MRR) which is a maximization objective
    f3 = x -> begin
        vc, fz, ap, ae = x
        MRR = (1000.0 * vc * fz * z * ap * ae) / (π * d)
        return -MRR  # Negated because it is a maximization objective
    end

    # Add objectives to the problem
    addObjective(problem, f1)
    addObjective(problem, f2)
    addObjective(problem, f3)

    return problem
end

```
## Gemini
```
function xu2020()
  problem = ContinuousProblem{Float64}("Xu2020")

  # Define number of variables and bounds
  n_vars = 4
  lower_bounds = [12.56, 0.02, 1.0, 0.5]
  upper_bounds = [25.12, 0.06, 5.0, 2.0]
  for i in 1:n_vars
    addVariable(problem, Bounds{Float64}(lower_bounds[i], upper_bounds[i]))
  end

  # Define objective functions (assuming d=2.5 and z=1.0)
  f1 = x -> begin  # Ft (Minimization)
    vc, fz, ap, ae = x
    Ft = -54.3 - 1.18 * vc - 2429 * fz + 104.2 * ap + 129.0 * ae
         - 18.9 * vc * fz - 0.209 * vc * ap - 0.673 * vc * ae + 265 * fz * ap
         + 1209 * fz * ae + 22.76 * ap * ae + 0.066 * vc^2
         + 32117 * fz^2 - 16.98 * ap^2 - 47.6 * ae^2
    return Ft
  end

  f2 = x -> begin  # Ra (Minimization)
    vc, fz, ap, ae = x
    Ra = 0.227 - 0.0072 * vc + 1.89 * fz - 0.0203 * ap + 0.3075 * ae
         - 0.198 * vc * fz - 0.000955 * vc * ap - 0.00656 * vc * ae
         + 0.209 * fz * ap + 0.783 * fz * ae + 0.02275 * ap * ae
         + 0.000355 * vc^2 + 35 * fz^2 + 0.00037 * ap^2 - 0.0791 * ae^2
    return Ra
  end

  f3 = x -> begin  # MRR (Maximization)
    vc, fz, ap, ae = x
    MRR = (1000.0 * vc * fz * 1.0 * ap * ae) / (pi * 2.5)  # Assuming z=1.0 and d=2.5
    return -MRR
  end

  addObjective(problem, f1)
  addObjective(problem, f2)
  addObjective(problem, f3)

  return problem
end
```
# RWA7: Goel2007
## Petición
```
This is the Java implementation for the Fonseca problem:
package org.uma.jmetal.problem.multiobjective;

import java.util.ArrayList;
import java.util.List;
import org.uma.jmetal.problem.doubleproblem.impl.AbstractDoubleProblem;
import org.uma.jmetal.solution.doublesolution.DoubleSolution;

/**
 * Class representing problem Fonseca
 */
@SuppressWarnings("serial")
public class Fonseca extends AbstractDoubleProblem {

  /**
   * Constructor
   */
  public Fonseca() {
    int numberOfVariables = 3;
    numberOfObjectives(2);
    numberOfConstraints(0);
    name("Fonseca");

    List<Double> lowerLimit = new ArrayList<>(numberOfVariables);
    List<Double> upperLimit = new ArrayList<>(numberOfVariables);

    for (int i = 0; i < numberOfVariables; i++) {
      lowerLimit.add(-4.0);
      upperLimit.add(4.0);
    }

    variableBounds(lowerLimit, upperLimit);
  }

  /**
   * Evaluate() method
   */
  @Override
  public DoubleSolution evaluate(DoubleSolution solution) {
    int numberOfVariables = numberOfVariables();

    double[] f = new double[solution.objectives().length];
    double[] x = new double[numberOfVariables];

    for (int i = 0; i < numberOfVariables; i++) {
      x[i] = solution.variables().get(i);
    }

    double sum1 = 0.0;
    for (int i = 0; i < numberOfVariables; i++) {
      sum1 += StrictMath.pow(x[i] - (1.0 / StrictMath.sqrt(numberOfVariables)), 2.0);
    }
    double exp1 = StrictMath.exp((-1.0) * sum1);
    f[0] = 1 - exp1;

    double sum2 = 0.0;
    for (int i = 0; i < numberOfVariables; i++) {
      sum2 += StrictMath.pow(x[i] + (1.0 / StrictMath.sqrt(numberOfVariables)), 2.0);
    }
    double exp2 = StrictMath.exp((-1.0) * sum2);
    f[1] = 1 - exp2;

    solution.objectives()[0] = f[0];
    solution.objectives()[1] = f[1];

    return solution;
  }
}


And this is how it is implemented in Julia:
function fonseca()
  problem = ContinuousProblem{Float64}("Fonseca")

  numberOfVariables = 3
  for _ in 1:numberOfVariables
    addVariable(problem, Bounds{Float64}(-4.0, 4.0))
  end

  f1 = x -> begin
    sum1 = 0.0
    for i in range(1, numberOfVariables)
      sum1 += ^(x[i] - (1.0 / sqrt(1.0 * numberOfVariables)), 2.0)
    end
    return 1.0 - exp(-1.0 * sum1)
  end

  f2 = x -> begin
    sum2 = 0.0
    for i in range(1, numberOfVariables)
      sum2 += ^(x[i] + (1.0 / sqrt(1.0 * numberOfVariables)), 2.0)
    end

    return 1.0 - exp(-1.0 * sum2)
  end

  addObjective(problem, f1)
  addObjective(problem, f2)

  return problem
end

Translate the Goel2007 problem to Julia, which implementation in Java is this:
package org.uma.jmetal.problem.multiobjective.rwa;


import java.util.ArrayList;
import java.util.List;
import org.uma.jmetal.problem.doubleproblem.impl.AbstractDoubleProblem;
import org.uma.jmetal.solution.doublesolution.DoubleSolution;

/**
 * Problem Goel2007 (RWA7) described in the paper "Engineering applications of
 * multi-objective evolutionary algorithms: A test suite of box-constrained real-world
 * problems". DOI: https://doi.org/10.1016/j.engappai.2023.106192
 */
public class Goel2007 extends AbstractDoubleProblem {

  public Goel2007() {
    numberOfObjectives(3);
    name("Goel2007");

    int numberOfVariables = 4 ;
    List<Double> lowerLimit = new ArrayList<>(numberOfVariables) ;
    List<Double> upperLimit = new ArrayList<>(numberOfVariables) ;

    for (int i = 0; i < numberOfVariables; i++) {
      lowerLimit.add(0.0);
      upperLimit.add(1.0);
    }

    variableBounds(lowerLimit, upperLimit);
  }


  @Override
  public DoubleSolution evaluate(DoubleSolution solution) {
    double a = solution.variables().get(0);
    double DHA = solution.variables().get(1);
    double DOA = solution.variables().get(2);
    double OPTT = solution.variables().get(3);
    double Xcc;
    double TFmax;
    double TTmax;

    Xcc = 0.153 - 0.322 * a + 0.396 * DHA + 0.424 * DOA + 0.0226 * OPTT
        + 0.175 * a * a + 0.0185 * DHA * a - 0.0701 * DHA * DHA
        - 0.251 * DOA * a + 0.179 * DOA * DHA + 0.0150 * DOA * DOA
        + 0.0134 * OPTT * a + 0.0296 * OPTT * DHA + 0.0752 * OPTT * DOA
        + 0.0192 * OPTT * OPTT;

    TFmax = 0.692 + 0.477 * a - 0.687 * DHA - 0.080 * DOA - 0.0650 * OPTT
        - 0.167 * a * a - 0.0129 * DHA * a + 0.0796 * DHA * DHA
        - 0.0634 * DOA * a - 0.0257 * DOA * DHA + 0.0877 * DOA * DOA
        - 0.0521 * OPTT * a + 0.00156 * OPTT * DHA + 0.00198 * OPTT * DOA
        + 0.0184 * OPTT * OPTT;

    TTmax = 0.370 - 0.205 * a + 0.0307 * DHA + 0.108 * DOA + 1.019 * OPTT
        - 0.135 * a * a + 0.0141 * DHA * a + 0.0998 * DHA * DHA
        + 0.208 * DOA * a - 0.0301 * DOA * DHA - 0.226 * DOA * DOA
        + 0.353 * OPTT * a - 0.0497 * OPTT * DOA - 0.423 * OPTT * OPTT
        + 0.202 * DHA * a * a - 0.281 * DOA * a * a - 0.342 * DHA * DHA * a
        - 0.245 * DHA * DHA * DOA + 0.281 * DOA * DOA * DHA
        - 0.184 * OPTT * OPTT * a - 0.281 * DHA * a * DOA;

    solution.objectives()[0] = Xcc;
    solution.objectives()[1] = TFmax;
    solution.objectives()[2] = TTmax;

    return solution ;
  }
}
```
## ChatGPT
```
function goel2007()
    problem = ContinuousProblem{Float64}("Goel2007")

    numberOfVariables = 4
    # Define variable bounds (0.0 to 1.0 for all variables)
    for _ in 1:numberOfVariables
        addVariable(problem, Bounds{Float64}(0.0, 1.0))
    end

    # Define the first objective (Xcc)
    f1 = x -> begin
        a, DHA, DOA, OPTT = x
        Xcc = 0.153 - 0.322 * a + 0.396 * DHA + 0.424 * DOA + 0.0226 * OPTT +
              0.175 * a^2 + 0.0185 * DHA * a - 0.0701 * DHA^2 -
              0.251 * DOA * a + 0.179 * DOA * DHA + 0.0150 * DOA^2 +
              0.0134 * OPTT * a + 0.0296 * OPTT * DHA + 0.0752 * OPTT * DOA +
              0.0192 * OPTT^2
        return Xcc
    end

    # Define the second objective (TFmax)
    f2 = x -> begin
        a, DHA, DOA, OPTT = x
        TFmax = 0.692 + 0.477 * a - 0.687 * DHA - 0.080 * DOA - 0.0650 * OPTT -
                0.167 * a^2 - 0.0129 * DHA * a + 0.0796 * DHA^2 -
                0.0634 * DOA * a - 0.0257 * DOA * DHA + 0.0877 * DOA^2 -
                0.0521 * OPTT * a + 0.00156 * OPTT * DHA + 0.00198 * OPTT * DOA +
                0.0184 * OPTT^2
        return TFmax
    end

    # Define the third objective (TTmax)
    f3 = x -> begin
        a, DHA, DOA, OPTT = x
        TTmax = 0.370 - 0.205 * a + 0.0307 * DHA + 0.108 * DOA + 1.019 * OPTT -
                0.135 * a^2 + 0.0141 * DHA * a + 0.0998 * DHA^2 +
                0.208 * DOA * a - 0.0301 * DOA * DHA - 0.226 * DOA^2 +
                0.353 * OPTT * a - 0.0497 * OPTT * DOA - 0.423 * OPTT^2 +
                0.202 * DHA * a^2 - 0.281 * DOA * a^2 - 0.342 * DHA^2 * a -
                0.245 * DHA^2 * DOA + 0.281 * DOA^2 * DHA -
                0.184 * OPTT^2 * a - 0.281 * DHA * a * DOA
        return TTmax
    end

    # Add objectives to the problem
    addObjective(problem, f1)
    addObjective(problem, f2)
    addObjective(problem, f3)

    return problem
end

```
## Gemini
```
function goel2007()
  problem = ContinuousProblem{Float64}("Goel2007")

  # Define number of variables and bounds
  n_vars = 4
  lower_bounds = [0.0, 0.0, 0.0, 0.0]
  upper_bounds = [1.0, 1.0, 1.0, 1.0]
  for i in 1:n_vars
    addVariable(problem, Bounds{Float64}(lower_bounds[i], upper_bounds[i]))
  end

  # Define objective functions
  f1 = x -> begin  # Xcc
    a, DHA, DOA, OPTT = x
    Xcc = 0.153 - 0.322 * a + 0.396 * DHA + 0.424 * DOA + 0.0226 * OPTT
          + 0.175 * a^2 + 0.0185 * DHA * a - 0.0701 * DHA^2
          - 0.251 * DOA * a + 0.179 * DOA * DHA + 0.0150 * DOA^2
          + 0.0134 * OPTT * a + 0.0296 * OPTT * DHA + 0.0752 * OPTT * DOA
          + 0.0192 * OPTT^2
    return Xcc
  end

  f2 = x -> begin  # TFmax
    a, DHA, DOA, OPTT = x
    TFmax = 0.692 + 0.477 * a - 0.687 * DHA - 0.080 * DOA - 0.0650 * OPTT
           - 0.167 * a^2 - 0.0129 * DHA * a + 0.0796 * DHA^2
           - 0.0634 * DOA * a - 0.0257 * DOA * DHA + 0.0877 * DOA^2
           - 0.0521 * OPTT * a + 0.00156 * OPTT * DHA + 0.00198 * OPTT * DOA
           + 0.0184 * OPTT^2
    return TFmax
  end

  f3 = x -> begin  # TTmax
    a, DHA, DOA, OPTT = x
    TTmax = 0.370 - 0.205 * a + 0.0307 * DHA + 0.108 * DOA + 1.019 * OPTT
           - 0.135 * a^2 + 0.0141 * DHA * a + 0.0998 * DHA^2
           + 0.208 * DOA * a - 0.0301 * DOA * DHA - 0.226 * DOA^2
           + 0.353 * OPTT * a - 0.0497 * OPTT * DOA - 0.423 * OPTT^2
           + 0.202 * DHA * a^2 - 0.281 * DOA * a^2 - 0.342 * DHA * DHA * a
           - 0.245 * DHA * DHA * DOA + 0.281 * DOA * DOA * DHA
           - 0.184 * OPTT * OPTT * a - 0.281 * DHA * a * DOA
    return TTmax
  end

  addObjective(problem, f1)
  addObjective(problem, f2)
  addObjective(problem, f3)

  return problem
end
```
# RWA8: Vaidyanathan2004
## Petición
```
This is the Java implementation for the Fonseca problem:
package org.uma.jmetal.problem.multiobjective;

import java.util.ArrayList;
import java.util.List;
import org.uma.jmetal.problem.doubleproblem.impl.AbstractDoubleProblem;
import org.uma.jmetal.solution.doublesolution.DoubleSolution;

/**
 * Class representing problem Fonseca
 */
@SuppressWarnings("serial")
public class Fonseca extends AbstractDoubleProblem {

  /**
   * Constructor
   */
  public Fonseca() {
    int numberOfVariables = 3;
    numberOfObjectives(2);
    numberOfConstraints(0);
    name("Fonseca");

    List<Double> lowerLimit = new ArrayList<>(numberOfVariables);
    List<Double> upperLimit = new ArrayList<>(numberOfVariables);

    for (int i = 0; i < numberOfVariables; i++) {
      lowerLimit.add(-4.0);
      upperLimit.add(4.0);
    }

    variableBounds(lowerLimit, upperLimit);
  }

  /**
   * Evaluate() method
   */
  @Override
  public DoubleSolution evaluate(DoubleSolution solution) {
    int numberOfVariables = numberOfVariables();

    double[] f = new double[solution.objectives().length];
    double[] x = new double[numberOfVariables];

    for (int i = 0; i < numberOfVariables; i++) {
      x[i] = solution.variables().get(i);
    }

    double sum1 = 0.0;
    for (int i = 0; i < numberOfVariables; i++) {
      sum1 += StrictMath.pow(x[i] - (1.0 / StrictMath.sqrt(numberOfVariables)), 2.0);
    }
    double exp1 = StrictMath.exp((-1.0) * sum1);
    f[0] = 1 - exp1;

    double sum2 = 0.0;
    for (int i = 0; i < numberOfVariables; i++) {
      sum2 += StrictMath.pow(x[i] + (1.0 / StrictMath.sqrt(numberOfVariables)), 2.0);
    }
    double exp2 = StrictMath.exp((-1.0) * sum2);
    f[1] = 1 - exp2;

    solution.objectives()[0] = f[0];
    solution.objectives()[1] = f[1];

    return solution;
  }
}


And this is how it is implemented in Julia:
function fonseca()
  problem = ContinuousProblem{Float64}("Fonseca")

  numberOfVariables = 3
  for _ in 1:numberOfVariables
    addVariable(problem, Bounds{Float64}(-4.0, 4.0))
  end

  f1 = x -> begin
    sum1 = 0.0
    for i in range(1, numberOfVariables)
      sum1 += ^(x[i] - (1.0 / sqrt(1.0 * numberOfVariables)), 2.0)
    end
    return 1.0 - exp(-1.0 * sum1)
  end

  f2 = x -> begin
    sum2 = 0.0
    for i in range(1, numberOfVariables)
      sum2 += ^(x[i] + (1.0 / sqrt(1.0 * numberOfVariables)), 2.0)
    end

    return 1.0 - exp(-1.0 * sum2)
  end

  addObjective(problem, f1)
  addObjective(problem, f2)

  return problem
end

Translate the Vaidyanathan2004 problem to Julia, which implementation in Java is this:
package org.uma.jmetal.problem.multiobjective.rwa;


import java.util.ArrayList;
import java.util.List;
import org.uma.jmetal.problem.doubleproblem.impl.AbstractDoubleProblem;
import org.uma.jmetal.solution.doublesolution.DoubleSolution;

/**
 * Problem Vaidyanathan2004 (RWA8) described in the paper "Engineering applications of
 * multi-objective evolutionary algorithms: A test suite of box-constrained real-world
 * problems". DOI: https://doi.org/10.1016/j.engappai.2023.106192
 */
public class Vaidyanathan2004 extends AbstractDoubleProblem {

  public Vaidyanathan2004() {
    numberOfObjectives(4) ;
    name("Vaidyanathan2004") ;

    int numberOfVariables = 4 ;
    List<Double> lowerLimit = new ArrayList<>(numberOfVariables) ;
    List<Double> upperLimit = new ArrayList<>(numberOfVariables) ;

    for (int i = 0; i < numberOfVariables; i++) {
      lowerLimit.add(0.0);
      upperLimit.add(1.0);
    }

    variableBounds(lowerLimit, upperLimit);
  }

  @Override
  public DoubleSolution evaluate(DoubleSolution solution) {
    double a = solution.variables().get(0);
    double DHA = solution.variables().get(1);
    double DOA = solution.variables().get(2);
    double OPTT = solution.variables().get(3);

    double Xcc;
    double TFmax;
    double TTmax;
    double TW4;

    // TFmax (minimization)
    TFmax = 0.692 + 0.477 * a - 0.687 * DHA - 0.080 * DOA - 0.0650 * OPTT
        - 0.167 * a * a - 0.0129 * DHA * a + 0.0796 * DHA * DHA
        - 0.0634 * DOA * a - 0.0257 * DOA * DHA + 0.0877 * DOA * DOA
        - 0.0521 * OPTT * a + 0.00156 * OPTT * DHA + 0.00198 * OPTT * DOA
        + 0.0184 * OPTT * OPTT;

    // Xcc (minimization)
    Xcc = 0.153 - 0.322 * a + 0.396 * DHA + 0.424 * DOA + 0.0226 * OPTT
        + 0.175 * a * a + 0.0185 * DHA * a - 0.0701 * DHA * DHA
        - 0.251 * DOA * a + 0.179 * DOA * DHA + 0.0150 * DOA * DOA
        + 0.0134 * OPTT * a + 0.0296 * OPTT * DHA + 0.0752 * OPTT * DOA
        + 0.0192 * OPTT * OPTT;

    // TW4 (minimization)
    TW4 = 0.758 + 0.358 * a - 0.807 * DHA + 0.0925 * DOA - 0.0468 * OPTT
        - 0.172 * a * a + 0.0106 * DHA * a + 0.0697 * DHA * DHA
        - 0.146 * DOA * a - 0.0416 * DOA * DHA + 0.102 * DOA * DOA
        - 0.0694 * OPTT * a - 0.00503 * OPTT * DHA + 0.0151 * OPTT * DOA
        + 0.0173 * OPTT * OPTT;

    // TTmax (minimization)
    TTmax = 0.370 - 0.205 * a + 0.0307 * DHA + 0.108 * DOA + 1.019 * OPTT
        - 0.135 * a * a + 0.0141 * DHA * a + 0.0998 * DHA * DHA
        + 0.208 * DOA * a - 0.0301 * DOA * DHA - 0.226 * DOA * DOA
        + 0.353 * OPTT * a - 0.0497 * OPTT * DOA - 0.423 * OPTT * OPTT
        + 0.202 * DHA * a * a - 0.281 * DOA * a * a - 0.342 * DHA * DHA * a
        - 0.245 * DHA * DHA * DOA + 0.281 * DOA * DOA * DHA
        - 0.184 * OPTT * OPTT * a - 0.281 * DHA * a * DOA;

    solution.objectives()[0] = TFmax; /* Minimization */
    solution.objectives()[1] = TW4; /* Minimization */
    solution.objectives()[2] = TTmax; /* Minimization */
    solution.objectives()[3] = Xcc; /* Minimization */

    return solution ;
  }
}
```
## ChatGPT
```
function vaidyanathan2004()
    problem = ContinuousProblem{Float64}("Vaidyanathan2004")

    numberOfVariables = 4
    # Define variable bounds (0.0 to 1.0 for all variables)
    for _ in 1:numberOfVariables
        addVariable(problem, Bounds{Float64}(0.0, 1.0))
    end

    # Define the first objective (TFmax)
    f1 = x -> begin
        a, DHA, DOA, OPTT = x
        TFmax = 0.692 + 0.477 * a - 0.687 * DHA - 0.080 * DOA - 0.0650 * OPTT -
                0.167 * a^2 - 0.0129 * DHA * a + 0.0796 * DHA^2 -
                0.0634 * DOA * a - 0.0257 * DOA * DHA + 0.0877 * DOA^2 -
                0.0521 * OPTT * a + 0.00156 * OPTT * DHA + 0.00198 * OPTT * DOA +
                0.0184 * OPTT^2
        return TFmax
    end

    # Define the second objective (TW4)
    f2 = x -> begin
        a, DHA, DOA, OPTT = x
        TW4 = 0.758 + 0.358 * a - 0.807 * DHA + 0.0925 * DOA - 0.0468 * OPTT -
              0.172 * a^2 + 0.0106 * DHA * a + 0.0697 * DHA^2 -
              0.146 * DOA * a - 0.0416 * DOA * DHA + 0.102 * DOA^2 -
              0.0694 * OPTT * a - 0.00503 * OPTT * DHA + 0.0151 * OPTT * DOA +
              0.0173 * OPTT^2
        return TW4
    end

    # Define the third objective (TTmax)
    f3 = x -> begin
        a, DHA, DOA, OPTT = x
        TTmax = 0.370 - 0.205 * a + 0.0307 * DHA + 0.108 * DOA + 1.019 * OPTT -
                0.135 * a^2 + 0.0141 * DHA * a + 0.0998 * DHA^2 +
                0.208 * DOA * a - 0.0301 * DOA * DHA - 0.226 * DOA^2 +
                0.353 * OPTT * a - 0.0497 * OPTT * DOA - 0.423 * OPTT^2 +
                0.202 * DHA * a^2 - 0.281 * DOA * a^2 - 0.342 * DHA^2 * a -
                0.245 * DHA^2 * DOA + 0.281 * DOA^2 * DHA -
                0.184 * OPTT^2 * a - 0.281 * DHA * a * DOA
        return TTmax
    end

    # Define the fourth objective (Xcc)
    f4 = x -> begin
        a, DHA, DOA, OPTT = x
        Xcc = 0.153 - 0.322 * a + 0.396 * DHA + 0.424 * DOA + 0.0226 * OPTT +
              0.175 * a^2 + 0.0185 * DHA * a - 0.0701 * DHA^2 -
              0.251 * DOA * a + 0.179 * DOA * DHA + 0.0150 * DOA^2 +
              0.0134 * OPTT * a + 0.0296 * OPTT * DHA + 0.0752 * OPTT * DOA +
              0.0192 * OPTT^2
        return Xcc
    end

    # Add objectives to the problem
    addObjective(problem, f1)
    addObjective(problem, f2)
    addObjective(problem, f3)
    addObjective(problem, f4)

    return problem
end

```
## Gemini
```
function vaidyanathan2004()
  problem = ContinuousProblem{Float64}("Vaidyanathan2004")

  # Define number of variables and bounds
  n_vars = 4
  lower_bounds = [0.0, 0.0, 0.0, 0.0]
  upper_bounds = [1.0, 1.0, 1.0, 1.0]
  for i in 1:n_vars
    addVariable(problem, Bounds{Float64}(lower_bounds[i], upper_bounds[i]))
  end

  # Define objective functions
  f1 = x -> begin  # TFmax (Minimization)
    a, DHA, DOA, OPTT = x
    TFmax = 0.692 + 0.477 * a - 0.687 * DHA - 0.080 * DOA - 0.0650 * OPTT
           - 0.167 * a^2 - 0.0129 * DHA * a + 0.0796 * DHA^2
           - 0.0634 * DOA * a - 0.0257 * DOA * DHA + 0.0877 * DOA^2
           - 0.0521 * OPTT * a + 0.00156 * OPTT * DHA + 0.00198 * OPTT * DOA
           + 0.0184 * OPTT^2
    return TFmax
  end

  f2 = x -> begin  # TW4 (Minimization)
    a, DHA, DOA, OPTT = x
    TW4 = 0.758 + 0.358 * a - 0.807 * DHA + 0.0925 * DOA - 0.0468 * OPTT
           - 0.172 * a^2 + 0.0106 * DHA * a + 0.0697 * DHA^2
           - 0.146 * DOA * a - 0.0416 * DOA * DHA + 0.102 * DOA^2
           - 0.0694 * OPTT * a - 0.00503 * OPTT * DHA + 0.0151 * OPTT * DOA
           + 0.0173 * OPTT^2
    return TW4
  end

  f3 = x -> begin  # TTmax (Minimization)
    a, DHA, DOA, OPTT = x
    TTmax = 0.370 - 0.205 * a + 0.0307 * DHA + 0.108 * DOA + 1.019 * OPTT
           - 0.135 * a^2 + 0.0141 * DHA * a + 0.0998 * DHA^2
           + 0.208 * DOA * a - 0.0301 * DOA * DHA - 0.226 * DOA^2
           + 0.353 * OPTT * a - 0.0497 * OPTT * DOA - 0.423 * OPTT^2
           + 0.202 * DHA * a^2 - 0.281 * DOA * a^2 - 0.342 * DHA * DHA * a
           - 0.245 * DHA * DHA * DOA + 0.281 * DOA * DOA * DHA
           - 0.184 * OPTT * OPTT * a - 0.281 * DHA * a * DOA
    return TTmax
  end

  f4 = x -> begin  # Xcc (Minimization)
    a, DHA, DOA, OPTT = x
    Xcc = 0.153 - 0.322 * a + 0.396 * DHA + 0.424 * DOA + 0.0226 * OPTT
          + 0.175 * a^2 + 0.0185 * DHA * a -
```
# RWA9: Chen2015
## Petición
```
This is the Java implementation for the Fonseca problem:
package org.uma.jmetal.problem.multiobjective;

import java.util.ArrayList;
import java.util.List;
import org.uma.jmetal.problem.doubleproblem.impl.AbstractDoubleProblem;
import org.uma.jmetal.solution.doublesolution.DoubleSolution;

/**
 * Class representing problem Fonseca
 */
@SuppressWarnings("serial")
public class Fonseca extends AbstractDoubleProblem {

  /**
   * Constructor
   */
  public Fonseca() {
    int numberOfVariables = 3;
    numberOfObjectives(2);
    numberOfConstraints(0);
    name("Fonseca");

    List<Double> lowerLimit = new ArrayList<>(numberOfVariables);
    List<Double> upperLimit = new ArrayList<>(numberOfVariables);

    for (int i = 0; i < numberOfVariables; i++) {
      lowerLimit.add(-4.0);
      upperLimit.add(4.0);
    }

    variableBounds(lowerLimit, upperLimit);
  }

  /**
   * Evaluate() method
   */
  @Override
  public DoubleSolution evaluate(DoubleSolution solution) {
    int numberOfVariables = numberOfVariables();

    double[] f = new double[solution.objectives().length];
    double[] x = new double[numberOfVariables];

    for (int i = 0; i < numberOfVariables; i++) {
      x[i] = solution.variables().get(i);
    }

    double sum1 = 0.0;
    for (int i = 0; i < numberOfVariables; i++) {
      sum1 += StrictMath.pow(x[i] - (1.0 / StrictMath.sqrt(numberOfVariables)), 2.0);
    }
    double exp1 = StrictMath.exp((-1.0) * sum1);
    f[0] = 1 - exp1;

    double sum2 = 0.0;
    for (int i = 0; i < numberOfVariables; i++) {
      sum2 += StrictMath.pow(x[i] + (1.0 / StrictMath.sqrt(numberOfVariables)), 2.0);
    }
    double exp2 = StrictMath.exp((-1.0) * sum2);
    f[1] = 1 - exp2;

    solution.objectives()[0] = f[0];
    solution.objectives()[1] = f[1];

    return solution;
  }
}


And this is how it is implemented in Julia:
function fonseca()
  problem = ContinuousProblem{Float64}("Fonseca")

  numberOfVariables = 3
  for _ in 1:numberOfVariables
    addVariable(problem, Bounds{Float64}(-4.0, 4.0))
  end

  f1 = x -> begin
    sum1 = 0.0
    for i in range(1, numberOfVariables)
      sum1 += ^(x[i] - (1.0 / sqrt(1.0 * numberOfVariables)), 2.0)
    end
    return 1.0 - exp(-1.0 * sum1)
  end

  f2 = x -> begin
    sum2 = 0.0
    for i in range(1, numberOfVariables)
      sum2 += ^(x[i] + (1.0 / sqrt(1.0 * numberOfVariables)), 2.0)
    end

    return 1.0 - exp(-1.0 * sum2)
  end

  addObjective(problem, f1)
  addObjective(problem, f2)

  return problem
end

Translate the Chen2015 problem to Julia, which implementation in Java is this:
package org.uma.jmetal.problem.multiobjective.rwa;


import java.util.List;
import org.uma.jmetal.problem.doubleproblem.impl.AbstractDoubleProblem;
import org.uma.jmetal.solution.doublesolution.DoubleSolution;

/**
 * Problem Chen2015 (RWA9) described in the paper "Engineering applications of
 * multi-objective evolutionary algorithms: A test suite of box-constrained real-world
 * problems". DOI: https://doi.org/10.1016/j.engappai.2023.106192
 */

public class Chen2015 extends AbstractDoubleProblem {

  public Chen2015() {
    numberOfObjectives(5);
    name("Chen2015");

    List<Double> lowerLimit = List.of(17.5, 17.5, 2.0, 2.0, 5.0, 5.0);
    List<Double> upperLimit = List.of(22.5, 22.5, 3.0, 3.0, 7.0, 6.0);

    variableBounds(lowerLimit, upperLimit);
  }

  @Override
  public DoubleSolution evaluate(DoubleSolution solution) {
    double l1 = solution.variables().get(0);
    double w1 = solution.variables().get(1);
    double l2 = solution.variables().get(2);
    double w2 = solution.variables().get(3);
    double a1 = solution.variables().get(4);
    double b1 = solution.variables().get(5);
    double a2 = l1 * w1 * l2 * w2;
    double b2 = l1 * w1 * l2 * a1;
    double d2 = w1 * w2 * a1 * b1;

    double F1, F2, F3, F4, F5;

    // F1 (minimization)
    F1 = 502.94 - 27.18 * ((w1 - 20.0) / 0.5) + 43.08 * ((l1 - 20.0) / 2.5)
        + 47.75 * (a1 - 6.0) + 32.25 * ((b1 - 5.5) / 0.5)
        + 31.67 * (a2 - 11.0)
        - 36.19 * ((w1 - 20.0) / 0.5) * ((w2 - 2.5) / 0.5)
        - 39.44 * ((w1 - 20.0) / 0.5) * (a1 - 6.0)
        + 57.45 * (a1 - 6.0) * ((b1 - 5.5) / 0.5);

    // F2 (maximization)
    F2 = (130.53 + 45.97 * ((l1 - 20.0) / 2.5) - 52.93 * ((w1 - 20.0) / 0.5)
        - 78.93 * (a1 - 6.0) + 79.22 * (a2 - 11.0)
        + 47.23 * ((w1 - 20.0) / 0.5) * (a1 - 6.0)
        - 40.61 * ((w1 - 20.0) / 0.5) * (a2 - 11.0)
        - 50.62 * (a1 - 6.0) * (a2 - 11.0));

    // F3 (maximization)
    F3 = (203.16 - 42.75 * ((w1 - 20.0) / 0.5) + 56.67 * (a1 - 6.0)
        + 19.88 * ((b1 - 5.5) / 0.5) - 12.89 * (a2 - 11.0)
        - 35.09 * (a1 - 6.0) * ((b1 - 5.5) / 0.5)
        - 22.91 * ((b1 - 5.5) / 0.5) * (a2 - 11.0));

    // F4 (maximization)
    F4 = (0.76 - 0.06 * ((l1 - 20.0) / 2.5) + 0.03 * ((l2 - 2.5) / 0.5)
        + 0.02 * (a2 - 11.0) - 0.02 * ((b2 - 6.5) / 0.5)
        - 0.03 * ((d2 - 12.0) / 0.5)
        + 0.03 * ((l1 - 20.0) / 2.5) * ((w1 - 20.0) / 0.5)
        - 0.02 * ((l1 - 20.0) / 2.5) * ((l2 - 2.5) / 0.5)
        + 0.02 * ((l1 - 20.0) / 2.5) * ((b2 - 6.5) / 0.5));

    // F5 (minimization)
    F5 = 1.08 - 0.12 * ((l1 - 20.0) / 2.5) - 0.26 * ((w1 - 20.0) / 0.5)
        - 0.05 * (a2 - 11.0) - 0.12 * ((b2 - 6.5) / 0.5)
        + 0.08 * (a1 - 6.0) * ((b2 - 6.5) / 0.5)
        + 0.07 * (a2 - 6.0) * ((b2 - 5.5) / 0.5);

    solution.objectives()[0] = F1; // minimization
    solution.objectives()[1] = -F2; // maximization
    solution.objectives()[2] = -F3; // maximization
    solution.objectives()[3] = -F4; // maximization
    solution.objectives()[4] = F5; // minimization

    return solution ;
  }
}
```
## ChatGPT
```
function chen2015()
    problem = ContinuousProblem{Float64}("Chen2015")

    # Define the variable bounds
    lower_limits = [17.5, 17.5, 2.0, 2.0, 5.0, 5.0]
    upper_limits = [22.5, 22.5, 3.0, 3.0, 7.0, 6.0]

    for i in 1:6
        addVariable(problem, Bounds{Float64}(lower_limits[i], upper_limits[i]))
    end

    # F1 (minimization)
    f1 = x -> begin
        l1, w1, l2, w2, a1, b1 = x[1], x[2], x[3], x[4], x[5], x[6]
        a2 = l1 * w1 * l2 * w2
        return 502.94 - 27.18 * ((w1 - 20.0) / 0.5) + 43.08 * ((l1 - 20.0) / 2.5) +
            47.75 * (a1 - 6.0) + 32.25 * ((b1 - 5.5) / 0.5) + 31.67 * (a2 - 11.0) -
            36.19 * ((w1 - 20.0) / 0.5) * ((w2 - 2.5) / 0.5) - 39.44 * ((w1 - 20.0) / 0.5) * (a1 - 6.0) +
            57.45 * (a1 - 6.0) * ((b1 - 5.5) / 0.5)
    end

    # F2 (maximization)
    f2 = x -> begin
        l1, w1, l2, w2, a1, b1 = x[1], x[2], x[3], x[4], x[5], x[6]
        a2 = l1 * w1 * l2 * w2
        return -(130.53 + 45.97 * ((l1 - 20.0) / 2.5) - 52.93 * ((w1 - 20.0) / 0.5) -
            78.93 * (a1 - 6.0) + 79.22 * (a2 - 11.0) + 47.23 * ((w1 - 20.0) / 0.5) * (a1 - 6.0) -
            40.61 * ((w1 - 20.0) / 0.5) * (a2 - 11.0) - 50.62 * (a1 - 6.0) * (a2 - 11.0))
    end

    # F3 (maximization)
    f3 = x -> begin
        l1, w1, l2, w2, a1, b1 = x[1], x[2], x[3], x[4], x[5], x[6]
        a2 = l1 * w1 * l2 * w2
        return -(203.16 - 42.75 * ((w1 - 20.0) / 0.5) + 56.67 * (a1 - 6.0) + 
            19.88 * ((b1 - 5.5) / 0.5) - 12.89 * (a2 - 11.0) - 35.09 * (a1 - 6.0) * ((b1 - 5.5) / 0.5) - 
            22.91 * ((b1 - 5.5) / 0.5) * (a2 - 11.0))
    end

    # F4 (maximization)
    f4 = x -> begin
        l1, w1, l2, w2, a1, b1 = x[1], x[2], x[3], x[4], x[5], x[6]
        a2 = l1 * w1 * l2 * w2
        b2 = l1 * w1 * l2 * a1
        d2 = w1 * w2 * a1 * b1
        return -(0.76 - 0.06 * ((l1 - 20.0) / 2.5) + 0.03 * ((l2 - 2.5) / 0.5) + 0.02 * (a2 - 11.0) -
            0.02 * ((b2 - 6.5) / 0.5) - 0.03 * ((d2 - 12.0) / 0.5) + 
            0.03 * ((l1 - 20.0) / 2.5) * ((w1 - 20.0) / 0.5) - 0.02 * ((l1 - 20.0) / 2.5) * ((l2 - 2.5) / 0.5) + 
            0.02 * ((l1 - 20.0) / 2.5) * ((b2 - 6.5) / 0.5))
    end

    # F5 (minimization)
    f5 = x -> begin
        l1, w1, l2, w2, a1, b1 = x[1], x[2], x[3], x[4], x[5], x[6]
        a2 = l1 * w1 * l2 * w2
        b2 = l1 * w1 * l2 * a1
        return 1.08 - 0.12 * ((l1 - 20.0) / 2.5) - 0.26 * ((w1 - 20.0) / 0.5) - 
            0.05 * (a2 - 11.0) - 0.12 * ((b2 - 6.5) / 0.5) + 0.08 * (a1 - 6.0) * ((b2 - 6.5) / 0.5) + 
            0.07 * (a2 - 6.0) * ((b2 - 5.5) / 0.5)
    end

    # Add objectives to the problem
    addObjective(problem, f1)
    addObjective(problem, f2)
    addObjective(problem, f3)
    addObjective(problem, f4)
    addObjective(problem, f5)

    return problem
end

```
## Gemini
```
function chen2015()
  problem = ContinuousProblem{Float64}("Chen2015")

  # Define number of variables and bounds
  n_vars = 6
  lower_bounds = [17.5, 17.5, 2.0, 2.0, 5.0, 5.0]
  upper_bounds = [22.5, 22.5, 3.0, 3.0, 7.0, 6.0]
  for i in 1:n_vars
    addVariable(problem, Bounds{Float64}(lower_bounds[i], upper_bounds[i]))
  end

  # Define objective functions
  f1 = x -> begin  # F1 (minimization)
    l1, w1, l2, w2, a1, b1 = x
    a2 = l1 * w1 * l2 * w2
    b2 = l1 * w1 * l2 * a1
    d2 = w1 * w2 * a1 * b1
    F1 = 502.94 - 27.18 * ((w1 - 20.0) / 0.5) + 43.08 * ((l1 - 20.0) / 2.5)
         + 47.75 * (a1 - 6.0) + 32.25 * ((b1 - 5.5) / 0.5)
         + 31.67 * (a2 - 11.0)
         - 36.19 * ((w1 - 20.0) / 0.5) * ((w2 - 2.5) / 0.5)
         - 39.44 * ((w1 - 20.0) / 0.5) * (a1 - 6.0)
         + 57.45 * (a1 - 6.0) * ((b1 - 5.5) / 0.5)
    return F1
  end

  f2 = x -> begin  # F2 (maximization)
    l1, w1, l2, w2, a1, b1 = x
    a2 = l1 * w1 * l2 * w2
    F2 = (130.53 + 45.97 * ((l1 - 20.0) / 2.5) - 52.93 * ((w1 - 20.0) / 0.5)
         - 78.93 * (a1 - 6.0) + 79.22 * (a2 - 11.0)
         + 47.23 * ((w1 - 20.0) / 0.5) * (a1 - 6.0)
         - 40.61 * ((w1 - 20.0) / 0.5) * (a2 - 11.0)
         - 50.62 * (a1 - 6.0) * (a2 - 11.0))
    return F2
  end

  f3 = x -> begin  # F3 (maximization)
    l1, w1, l2, w2, a1, b1 = x
    a2 = l1 * w1 * l2 * w2
    F3 = (203.16 - 42.75 * ((w1 - 20.0) / 0.5) + 56.67 * (a1 - 6.0)
         + 19.88 * ((b1 - 5.5) / 0.5) - 12.89 * (a2 - 11.0)
         - 35.09 * (a1 - 6.0) * ((b1 - 5.5) / 0.5)
         - 22.91 * ((b1 - 5.5) / 0.5) * (a2 - 11.0))
    return F3
  end

  f4 = x -> begin  # F4 (maximization)
    l1, w1,
```
# RWA10: Ahmad2017
## Petición
```
function chen2015()
  problem = ContinuousProblem{Float64}("Chen2015")

  # Define number of variables and bounds
  n_vars = 6
  lower_bounds = [17.5, 17.5, 2.0, 2.0, 5.0, 5.0]
  upper_bounds = [22.5, 22.5, 3.0, 3.0, 7.0, 6.0]
  for i in 1:n_vars
    addVariable(problem, Bounds{Float64}(lower_bounds[i], upper_bounds[i]))
  end

  # Define objective functions
  f1 = x -> begin  # F1 (minimization)
    l1, w1, l2, w2, a1, b1 = x
    a2 = l1 * w1 * l2 * w2
    b2 = l1 * w1 * l2 * a1
    d2 = w1 * w2 * a1 * b1
    F1 = 502.94 - 27.18 * ((w1 - 20.0) / 0.5) + 43.08 * ((l1 - 20.0) / 2.5)
         + 47.75 * (a1 - 6.0) + 32.25 * ((b1 - 5.5) / 0.5)
         + 31.67 * (a2 - 11.0)
         - 36.19 * ((w1 - 20.0) / 0.5) * ((w2 - 2.5) / 0.5)
         - 39.44 * ((w1 - 20.0) / 0.5) * (a1 - 6.0)
         + 57.45 * (a1 - 6.0) * ((b1 - 5.5) / 0.5)
    return F1
  end

  f2 = x -> begin  # F2 (maximization)
    l1, w1, l2, w2, a1, b1 = x
    a2 = l1 * w1 * l2 * w2
    F2 = (130.53 + 45.97 * ((l1 - 20.0) / 2.5) - 52.93 * ((w1 - 20.0) / 0.5)
         - 78.93 * (a1 - 6.0) + 79.22 * (a2 - 11.0)
         + 47.23 * ((w1 - 20.0) / 0.5) * (a1 - 6.0)
         - 40.61 * ((w1 - 20.0) / 0.5) * (a2 - 11.0)
         - 50.62 * (a1 - 6.0) * (a2 - 11.0))
    return F2
  end

  f3 = x -> begin  # F3 (maximization)
    l1, w1, l2, w2, a1, b1 = x
    a2 = l1 * w1 * l2 * w2
    F3 = (203.16 - 42.75 * ((w1 - 20.0) / 0.5) + 56.67 * (a1 - 6.0)
         + 19.88 * ((b1 - 5.5) / 0.5) - 12.89 * (a2 - 11.0)
         - 35.09 * (a1 - 6.0) * ((b1 - 5.5) / 0.5)
         - 22.91 * ((b1 - 5.5) / 0.5) * (a2 - 11.0))
    return F3
  end

  f4 = x -> begin  # F4 (maximization)
    l1, w1,
```
## ChatGPT
```
function ahmad2017()
    problem = ContinuousProblem{Float64}("Ahmad2017")

    numberOfVariables = 3

    # Add the variables with their respective bounds
    addVariable(problem, Bounds{Float64}(10.0, 50.0))  # X1
    addVariable(problem, Bounds{Float64}(10.0, 50.0))  # X2
    addVariable(problem, Bounds{Float64}(150.0, 170.0))  # X3

    # Objective 1: WCA (maximization)
    f1 = x -> begin
        WCA = -1331.04 + 1.99 * x[1] + 0.33 * x[2] + 17.12 * x[3] - 0.02 * x[1]^2 - 0.05 * x[3]^2 - 15.33
        return -WCA  # converting to minimization
    end

    # Objective 2: OCA (maximization)
    f2 = x -> begin
        OCA = -4231.14 + 4.27 * x[1] + 1.50 * x[2] + 52.30 * x[3] - 0.04 * x[1] * x[2] - 0.04 * x[1]^2 - 0.16 * x[3]^2 - 29.33
        return -OCA  # converting to minimization
    end

    # Objective 3: AP (maximization)
    f3 = x -> begin
        AP = 1766.80 - 32.32 * x[1] - 24.56 * x[2] - 10.48 * x[3] + 0.24 * x[1] * x[3] + 0.19 * x[2] * x[3] - 0.06 * x[1]^2 - 0.10 * x[2]^2 - 413.33
        return -AP  # converting to minimization
    end

    # Objective 4: CRA (maximization)
    f4 = x -> begin
        CRA = -2342.13 - 1.556 * x[1] + 0.77 * x[2] + 31.14 * x[3] + 0.03 * x[1]^2 - 0.10 * x[3]^2 - 73.33
        return -CRA  # converting to minimization
    end

    # Objective 5: Stiffness (minimization)
    f5 = x -> begin
        Stiffness = 9.34 + 0.02 * x[1] - 0.03 * x[2] - 0.03 * x[3] - 0.001 * x[1] * x[2] + 0.0009 * x[2]^2 + 0.22
        return Stiffness  # already a minimization problem
    end

    # Objective 6: Tear (maximization)
    f6 = x -> begin
        Tear = 1954.71 + 14.246 * x[1] + 5.00 * x[2] - 4.30 * x[3] - 0.22 * x[1]^2 - 0.33 * x[2]^2 - 8413.33
        return -Tear  # converting to minimization
    end

    # Objective 7: Tensile (maximization)
    f7 = x -> begin
        Tensile = 828.16 + 3.55 * x[1] + 73.65 * x[2] + 10.80 * x[3] - 0.56 * x[2] * x[3] + 0.20 * x[2]^2 - 2814.83
        return -Tensile  # converting to minimization
    end

    # Add the objectives to the problem
    addObjective(problem, f1)
    addObjective(problem, f2)
    addObjective(problem, f3)
    addObjective(problem, f4)
    addObjective(problem, f5)
    addObjective(problem, f6)
    addObjective(problem, f7)

    return problem
end

```
## Gemini
```
function ahmad2017()
  problem = ContinuousProblem{Float64}("Ahmad2017")

  # Define number of variables and bounds
  n_vars = 3
  lower_bounds = [10.0, 10.0, 150.0]
  upper_bounds = [50.0, 50.0, 170.0]
  for i in 1:n_vars
    addVariable(problem, Bounds{Float64}(lower_bounds[i], upper_bounds[i]))
  end

  # Define objective functions
  f1 = x -> begin  # WCA (maximization)
    X1, X2, X3 = x
    WCA = -1331.04 + 1.99 * X1 + 0.33 * X2 + 17.12 * X3 - 0.02 * X1^2 - 0.05 * X3^2 - 15.33
    return -WCA
  end

  f2 = x -> begin  # OCA (maximization)
    X1, X2, X3 = x
    OCA = -4231.14 + 4.27 * X1 + 1.50 * X2 + 52.30 * X3 - 0.04 * X1 * X2 - 0.04 * X1^2 - 0.16 * X3^2 - 29.33
    return -OCA
  end

  f3 = x -> begin  # AP (maximization)
    X1, X2, X3 = x
    AP = 1766.80 - 32.32 * X1 - 24.56 * X2 - 10.48 * X3 + 0.24 * X1 * X3 + 0.19 * X2 * X3 - 0.06 * X1^2 - 0.10 * X2^2 - 413.33
    return -AP
  end

  f4 = x -> begin  # CRA (maximization)
    X1, X2, X3 = x
    CRA = -2342.13 - 1.556 * X1 + 0.77 * X2 + 31.14 * X3 + 0.03 * X1^2 - 0.10 * X3^2 - 73.33
    return -CRA
  end

  f5 = x -> begin  # Stiffness (minimization)
    X1, X2, X3 = x
    Stiffness = 9.34 + 0.02 * X1 - 0.03 * X2 - 0.03 * X3 - 0.001 * X1 * X2 + 0.0009 * X2^2 + 0.22
    return Stiffness
  end

  f6 = x -> begin  # Tear (maximization)
    X1, X2, X3 = x
    Tear = 1954.71 + 14.246 * X1 + 5.00 * X2 - 4.30 * X3 - 0.22 * X1^2 - 0.33 * X2^2 - 8413.33
    return -Tear
  end

  f7 = x -> begin  # Tensile (maximization)
    X1, X2, X3 = x
    Tensile = 828.16 + 3.55 * X1 + 73.65 * X2 + 10.80 * X3 - 0.56 * X2 * X3 + 0.20 * X2^2 - 2814.83
    return -Tensile
  end

  # Add objective functions to the problem
  addObjective(problem, f1)
  addObjective(problem, f2)
  addObjective(problem, f3)
  addObjective(problem, f4)
  addObjective(problem, f5)
  addObjective(problem, f6)
  addObjective(problem, f7)

  return problem
end
```