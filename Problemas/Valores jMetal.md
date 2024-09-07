Código utilizado para obtener los valores de referencia usando jMetal:
```
package org.jmetal_test;
import org.uma.jmetal.problem.doubleproblem.impl.AbstractDoubleProblem;
import org.uma.jmetal.problem.multiobjective.rwa.*;
import org.uma.jmetal.solution.doublesolution.DoubleSolution;

import java.util.Arrays;

public class rwa_testing {
    public static void main(String[] args) {
        AbstractDoubleProblem[] problems = {
            new Subasi2016(),
            new Liao2008(),
            new Ganesan2013(),
            new Padhi2016(),
            new Gao2020(),
            new Xu2020(),
            new Goel2007(),
            new Vaidyanathan2004(),
            new Chen2015(),
            new Ahmad2017()
        };

        for (AbstractDoubleProblem problem : problems) {
            print_objectives(problem);
        }
    }
    
    private static void print_objectives(AbstractDoubleProblem problem) {
        int variables = problem.numberOfVariables();

        DoubleSolution solution1 = problem.createSolution();
        DoubleSolution solution2 = problem.createSolution();
        DoubleSolution solution3 = problem.createSolution();

        for (int i = 0; i < variables; i++) {
            double min = problem.variableBounds().get(i).getLowerBound();
            double max = problem.variableBounds().get(i).getUpperBound();
            double avg = (min + max) / 2;

            solution1.variables().set(i, min);
            solution2.variables().set(i, avg);
            solution3.variables().set(i, max);
        }

        evaluate_rwa(problem, solution1);
        evaluate_rwa(problem, solution2);
        evaluate_rwa(problem, solution3);
        System.out.println();
    }

    private static void evaluate_rwa(AbstractDoubleProblem problem, DoubleSolution solution) {
        DoubleSolution evaluated_solution = problem.evaluate(solution);
        System.out.println(problem.name() + solution.variables() + ": " + 
				        Arrays.toString(evaluated_solution.objectives()));
    }
}
```
Valores obtenidos para los objetivos:
```
Subasi2016[20.0, 6.0, 20.0, 0.0, 8000.0]: [-413.4309999999999, 0.58758]
Subasi2016[40.0, 10.5, 30.0, 15.0, 16500.0]: [-944.672, 6.893857499999998]
Subasi2016[60.0, 15.0, 40.0, 30.0, 25000.0]: [-1672.0169999999998, 20.595349999999996]

Liao2008[1.0, 1.0, 1.0, 1.0, 1.0]: [1661.7078224999998, 8.304599999999999, 0.0708]
Liao2008[2.0, 2.0, 2.0, 2.0, 2.0]: [1683.1333450000002, 9.626600000000002, 0.12329999999999995]
Liao2008[3.0, 3.0, 3.0, 3.0, 3.0]: [1704.5588675, 10.551600000000002, 0.10239999999999988]

Ganesan2013[0.25, 10000.0, 600.0]: [-42.60232653143125, -45.88523097531739, 0.44571826369883955]
Ganesan2013[0.4, 15000.0, 850.0]: [-85.395450682072, -46.38548351692535, 0.9801920249935423]
Ganesan2013[0.55, 20000.0, 1100.0]: [-142.90426239083925, -47.39579233891098, 1.9671532135129155]

Padhi2016[1.0, 10.0, 850.0, 20.0, 4.0]: [-1658.9999999999998, -195.328, -150.19519]
Padhi2016[1.2, 18.0, 1250.0, 30.0, 6.0]: [-3603.0108, -770.8981599999996, -370.73431632]
Padhi2016[1.4, 26.0, 1650.0, 40.0, 8.0]: [-6292.6431999999995, -1654.8934400000003, -687.1303800799999]

Gao2020[40.0, 0.35, 333.0, 20.0, 3000.0, 0.1, 308.0, 150.0, 0.1]: [1.5661026251024999E7, 9142503.345615, -4788.894955346499]
Gao2020[70.0, 0.425, 348.0, 30.0, 3500.0, 1.55, 318.0, 175.0, 1.05]: [2.1261026737381257E7, 1.374326221520375E7, -7124.653099324123]
Gao2020[100.0, 0.5, 363.0, 40.0, 4000.0, 3.0, 328.0, 200.0, 2.0]: [2.7720222637500003E7, 1.92093879955E7, -9843.80711015]

Xu2020[12.56, 0.02, 1.0, 0.5]: [62.548577599999994, 0.27887132800000003, -15.991888681873645]
Xu2020[18.84, 0.04, 3.0, 1.25]: [283.4725396, 0.34403403800000004, -359.817495342157]
Xu2020[25.12, 0.06, 5.0, 2.0]: [455.31855040000016, 0.3521081119999999, -1919.0266418248373]

Goel2007[0.0, 0.0, 0.0, 0.0]: [0.153, 0.692, 0.37]
Goel2007[0.5, 0.5, 0.5, 0.5]: [0.46425, 0.48153499999999994, 0.692875]
Goel2007[1.0, 1.0, 1.0, 1.0]: [0.8773999999999998, 0.20513999999999996, 0.2837999999999997]

Vaidyanathan2004[0.0, 0.0, 0.0, 0.0]: [0.692, 0.758, 0.37, 0.153]
Vaidyanathan2004[0.5, 0.5, 0.5, 0.5]: [0.48153499999999994, 0.5015175000000002, 0.692875, 0.46425]
Vaidyanathan2004[1.0, 1.0, 1.0, 1.0]: [0.20513999999999996, 0.13537000000000007, 0.2837999999999997, 0.8773999999999998]

Chen2015[17.5, 17.5, 2.0, 2.0, 5.0, 5.0]: [38642.44000000001, -404792.75, -12469.550000000001, 271.06000000000006, 520427.0200000001]
Chen2015[20.0, 20.0, 2.5, 2.5, 6.0, 5.5]: [79329.57, -197309.11, 31880.050000000003, 287.48, 2091477.81]
Chen2015[22.5, 22.5, 3.0, 3.0, 7.0, 6.0]: [144117.4875, 792849.7925, 162689.08000000002, 77.61500000000001, 6767897.0687500015]

Ahmad2017[10.0, 10.0, 150.0]: [-117.83000000000008, -34.22999999999949, 158.32999999999998, -0.6799999999997652, 4.949999999999998, 6966.16, 414.6700000000001]
Ahmad2017[30.0, 30.0, 160.0]: [-164.43000000000043, -112.63000000000004, 109.73000000000008, -10.359999999999602, 4.369999999999999, 7064.24, 450.6700000000001]
Ahmad2017[50.0, 50.0, 170.0]: [-185.03000000000011, -95.02999999999967, 17.130000000000166, -24.039999999999893, 3.7100000000000004, 7602.32, 550.6700000000001]
```