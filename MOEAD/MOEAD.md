```
This is the implementation in Java for NSGAIIBuilder:
package org.uma.jmetal.component.algorithm.multiobjective;

import java.util.Arrays;
import java.util.Comparator;
import org.uma.jmetal.component.algorithm.EvolutionaryAlgorithm;
import org.uma.jmetal.component.catalogue.common.evaluation.Evaluation;
import org.uma.jmetal.component.catalogue.common.evaluation.impl.SequentialEvaluation;
import org.uma.jmetal.component.catalogue.common.solutionscreation.SolutionsCreation;
import org.uma.jmetal.component.catalogue.common.solutionscreation.impl.RandomSolutionsCreation;
import org.uma.jmetal.component.catalogue.common.termination.Termination;
import org.uma.jmetal.component.catalogue.common.termination.impl.TerminationByEvaluations;
import org.uma.jmetal.component.catalogue.ea.replacement.Replacement;
import org.uma.jmetal.component.catalogue.ea.replacement.impl.RankingAndDensityEstimatorReplacement;
import org.uma.jmetal.component.catalogue.ea.selection.Selection;
import org.uma.jmetal.component.catalogue.ea.selection.impl.NaryTournamentSelection;
import org.uma.jmetal.component.catalogue.ea.variation.Variation;
import org.uma.jmetal.component.catalogue.ea.variation.impl.CrossoverAndMutationVariation;
import org.uma.jmetal.operator.crossover.CrossoverOperator;
import org.uma.jmetal.operator.mutation.MutationOperator;
import org.uma.jmetal.problem.Problem;
import org.uma.jmetal.solution.Solution;
import org.uma.jmetal.util.comparator.MultiComparator;
import org.uma.jmetal.util.densityestimator.DensityEstimator;
import org.uma.jmetal.util.densityestimator.impl.CrowdingDistanceDensityEstimator;
import org.uma.jmetal.util.ranking.Ranking;
import org.uma.jmetal.util.ranking.impl.FastNonDominatedSortRanking;

/**
 * Class to configure and build an instance of the NSGA-II algorithm
 *
 * @param <S>
 */
public class NSGAIIBuilder<S extends Solution<?>> {
  private String name;
  private Ranking<S> ranking;
  private DensityEstimator<S> densityEstimator;
  private Evaluation<S> evaluation;
  private SolutionsCreation<S> createInitialPopulation;
  private Termination termination;
  private Selection<S> selection;
  private Variation<S> variation;
  private Replacement<S> replacement;

  public NSGAIIBuilder(Problem<S> problem, int populationSize, int offspringPopulationSize,
      CrossoverOperator<S> crossover, MutationOperator<S> mutation) {
    name = "NSGAII";

    densityEstimator = new CrowdingDistanceDensityEstimator<>();
    ranking = new FastNonDominatedSortRanking<>();

    this.createInitialPopulation = new RandomSolutionsCreation<>(problem, populationSize);

    this.replacement =
        new RankingAndDensityEstimatorReplacement<>(
            ranking, densityEstimator, Replacement.RemovalPolicy.ONE_SHOT);

    this.variation =
        new CrossoverAndMutationVariation<>(
            offspringPopulationSize, crossover, mutation);

    int tournamentSize = 2 ;
    this.selection =
        new NaryTournamentSelection<>(
            tournamentSize,
            variation.getMatingPoolSize(),
            new MultiComparator<>(
                Arrays.asList(
                    Comparator.comparing(ranking::getRank),
                    Comparator.comparing(densityEstimator::value).reversed())));

    this.termination = new TerminationByEvaluations(25000);

    this.evaluation = new SequentialEvaluation<>(problem);
  }

  public NSGAIIBuilder<S> setTermination(Termination termination) {
    this.termination = termination;

    return this;
  }

  public NSGAIIBuilder<S> setRanking(Ranking<S> ranking) {
    this.ranking = ranking;
    this.replacement =
        new RankingAndDensityEstimatorReplacement<>(
            ranking, densityEstimator, Replacement.RemovalPolicy.ONE_SHOT);

    return this;
  }

  public NSGAIIBuilder<S> setEvaluation(Evaluation<S> evaluation) {
    this.evaluation = evaluation;

    return this;
  }

  public EvolutionaryAlgorithm<S> build() {
      return new EvolutionaryAlgorithm<>(name, createInitialPopulation, evaluation, termination,
          selection, variation, replacement);
  }
}

And this is the implementation in Julia:
using Dates

mutable struct NSGAII <: Algorithm
  problem::Problem
  populationSize::Int

  termination::Termination
  mutation::MutationOperator
  crossover::CrossoverOperator

  solver::EvolutionaryAlgorithm

  dominanceComparator::Comparator

  function NSGAII(
    problem :: ContinuousProblem; 
    populationSize = 100, 
    termination = TerminationByEvaluations(25000), dominanceComparator = numberOfConstraints(problem) == 0 ? DefaultDominanceComparator() : ConstraintsAndDominanceComparator(),
    crossover = SBXCrossover(probability = 1.0, distributionIndex = 20.0, bounds = problem.bounds),
    mutation = PolynomialMutation(probability = 1.0/numberOfVariables(problem), distributionIndex = 20.0, bounds = problem.bounds))
    algorithm = new()
    algorithm.solver = EvolutionaryAlgorithm()
    algorithm.solver.name = "NSGA-II"
    algorithm.problem = problem
    algorithm.populationSize = populationSize
    algorithm.dominanceComparator = dominanceComparator 
    algorithm.crossover = crossover
    algorithm.mutation = mutation
    algorithm.termination = termination

    return algorithm
  end

  function NSGAII(
    problem :: BinaryProblem; 
    populationSize = 100, 
    termination = TerminationByEvaluations(25000), dominanceComparator = numberOfConstraints(problem) == 0 ? DefaultDominanceComparator() : ConstraintsAndDominanceComparator(),
    crossover = SinglePointCrossover(probability = 1.0),
    mutation = BitFlipMutation(probability = 1.0/problem.numberOfBits))
    algorithm = new()
    algorithm.solver = EvolutionaryAlgorithm()
    algorithm.solver.name = "NSGA-II"
    algorithm.problem = problem
    algorithm.populationSize = populationSize
    algorithm.dominanceComparator = dominanceComparator 
    algorithm.crossover = crossover
    algorithm.mutation = mutation
    algorithm.termination = termination

    return algorithm
  end

end

function foundSolutions(nsgaII::NSGAII) 
  return nsgaII.solver.foundSolutions
end

function optimize!(nsgaII::NSGAII)
  solver = nsgaII.solver
  problem = nsgaII.problem

  populationSize = nsgaII.populationSize
  offspringPopulationSize = nsgaII.populationSize

  solver.solutionsCreation = DefaultSolutionsCreation(problem, populationSize)

  solver.evaluation = SequentialEvaluation(problem)

  solver.termination = nsgaII.termination
  solver.variation = CrossoverAndMutationVariation(offspringPopulationSize, nsgaII.crossover, nsgaII.mutation)

  solver.replacement = RankingAndDensityEstimatorReplacement(DominanceRanking(nsgaII.dominanceComparator), CrowdingDistanceDensityEstimator())

  solver.selection = BinaryTournamentSelection(solver.variation.matingPoolSize, nsgaII.dominanceComparator)

  return evolutionaryAlgorithm(solver)
end

function name(nsgaII::NSGAII)
  return name(nsgaII.solver)
end

function status(nsgaII::NSGAII)
  return status(nsgaII.solver)
end

function computingTime(nsgaII::NSGAII)
  return status(nsgaII)["COMPUTING_TIME"]
end

function observable(nsgaII::NSGAII)
  return observable(nsgaII.solver)
end


This is the implementation in Java for MOEADBuilder:
package org.uma.jmetal.component.algorithm.multiobjective;

import java.io.FileNotFoundException;
import org.uma.jmetal.component.algorithm.EvolutionaryAlgorithm;
import org.uma.jmetal.component.catalogue.common.evaluation.Evaluation;
import org.uma.jmetal.component.catalogue.common.evaluation.impl.SequentialEvaluation;
import org.uma.jmetal.component.catalogue.common.solutionscreation.SolutionsCreation;
import org.uma.jmetal.component.catalogue.common.solutionscreation.impl.RandomSolutionsCreation;
import org.uma.jmetal.component.catalogue.common.termination.Termination;
import org.uma.jmetal.component.catalogue.common.termination.impl.TerminationByEvaluations;
import org.uma.jmetal.component.catalogue.ea.replacement.Replacement;
import org.uma.jmetal.component.catalogue.ea.replacement.impl.MOEADReplacement;
import org.uma.jmetal.component.catalogue.ea.selection.Selection;
import org.uma.jmetal.component.catalogue.ea.selection.impl.PopulationAndNeighborhoodSelection;
import org.uma.jmetal.component.catalogue.ea.variation.Variation;
import org.uma.jmetal.component.catalogue.ea.variation.impl.CrossoverAndMutationVariation;
import org.uma.jmetal.operator.crossover.CrossoverOperator;
import org.uma.jmetal.operator.mutation.MutationOperator;
import org.uma.jmetal.problem.Problem;
import org.uma.jmetal.solution.Solution;
import org.uma.jmetal.util.aggregationfunction.AggregationFunction;
import org.uma.jmetal.util.aggregationfunction.impl.PenaltyBoundaryIntersection;
import org.uma.jmetal.util.neighborhood.impl.WeightVectorNeighborhood;
import org.uma.jmetal.util.sequencegenerator.SequenceGenerator;

/**
 * Class to configure and build an instance of the MOEA/D algorithm
 *
 * @param <S>
 */
public class MOEADBuilder<S extends Solution<?>> {

  private String name;
  private Evaluation<S> evaluation;
  private SolutionsCreation<S> createInitialPopulation;
  private Termination termination;
  private Selection<S> selection;
  private Variation<S> variation;
  private Replacement<S> replacement;
  private int neighborhoodSize = 20;
  private double neighborhoodSelectionProbability = 0.9;
  private int maximumNumberOfReplacedSolutions = 2;
  private String weightVectorDirectory;
  private AggregationFunction aggregationFunction;
  private SequenceGenerator<Integer> sequenceGenerator;
  private WeightVectorNeighborhood<S> neighborhood;
  private boolean normalize;

  public MOEADBuilder(Problem<S> problem, int populationSize,
      CrossoverOperator<S> crossover, MutationOperator<S> mutation, String weightVectorDirectory,
      SequenceGenerator<Integer> sequenceGenerator, boolean normalize) {
    name = "MOEAD";

    this.normalize = normalize;
    this.aggregationFunction = new PenaltyBoundaryIntersection(5.0, normalize);

    this.sequenceGenerator = sequenceGenerator;
    this.createInitialPopulation = new RandomSolutionsCreation<>(problem, populationSize);

    int offspringPopulationSize = 1;
    this.variation =
        new CrossoverAndMutationVariation<>(
            offspringPopulationSize, crossover, mutation);

    this.weightVectorDirectory = weightVectorDirectory;

    if (problem.numberOfObjectives() == 2) {
      neighborhood = new WeightVectorNeighborhood<>(populationSize, neighborhoodSize);
    } else {
      try {
        neighborhood =
            new WeightVectorNeighborhood<>(
                populationSize,
                problem.numberOfObjectives(),
                neighborhoodSize,
                this.weightVectorDirectory);
      } catch (FileNotFoundException exception) {
        exception.printStackTrace();
      }
    }

    this.selection =
        new PopulationAndNeighborhoodSelection<>(
            variation.getMatingPoolSize(),
            sequenceGenerator,
            neighborhood,
            neighborhoodSelectionProbability,
            true);

    this.replacement =
        new MOEADReplacement<>(
            (PopulationAndNeighborhoodSelection<S>) selection,
            neighborhood,
            aggregationFunction,
            sequenceGenerator,
            maximumNumberOfReplacedSolutions,
            this.normalize);

    this.termination = new TerminationByEvaluations(25000);

    this.evaluation = new SequentialEvaluation<>(problem);
  }

  public MOEADBuilder<S> setTermination(Termination termination) {
    this.termination = termination;

    return this;
  }

  public MOEADBuilder<S> setEvaluation(Evaluation<S> evaluation) {
    this.evaluation = evaluation;

    return this;
  }

  public MOEADBuilder<S> setNeighborhoodSelectionProbability(
      double neighborhoodSelectionProbability) {
    this.neighborhoodSelectionProbability = neighborhoodSelectionProbability;

    return this;
  }

  public MOEADBuilder<S> setMaximumNumberOfReplacedSolutionsy(
      int maximumNumberOfReplacedSolutions) {
    this.maximumNumberOfReplacedSolutions = maximumNumberOfReplacedSolutions;

    return this;
  }

  public MOEADBuilder<S> setNeighborhoodSize(int neighborhoodSize) {
    this.neighborhoodSize = neighborhoodSize;

    return this;
  }

  public MOEADBuilder<S> setAggregationFunction(AggregationFunction aggregationFunction) {
    this.aggregationFunction = aggregationFunction;

    this.replacement =
        new MOEADReplacement<>(
            (PopulationAndNeighborhoodSelection<S>) selection,
            neighborhood,
            this.aggregationFunction,
            sequenceGenerator,
            maximumNumberOfReplacedSolutions,
            aggregationFunction.normalizeObjectives());
    return this;
  }

  public MOEADBuilder<S> setVariation(Variation<S> variation) {
    this.variation = variation;

    return this;
  }

  public MOEADBuilder<S> setSelection(Selection<S> selection) {
    this.selection = selection;

    return this;
  }

  public EvolutionaryAlgorithm<S> build() {
    return new EvolutionaryAlgorithm<>(name, createInitialPopulation, evaluation, termination,
        selection, variation, replacement);
  }
}


Implement it in Julia.
```
## ChatGPT
```
mutable struct MOEAD <: Algorithm
    problem::Problem
    populationSize::Int
    offspringPopulationSize::Int
    neighborhoodSize::Int
    neighborhoodSelectionProbability::Float64
    maximumNumberOfReplacedSolutions::Int
    weightVectorDirectory::String
    aggregationFunction::AggregationFunction
    sequenceGenerator::SequenceGenerator
    normalize::Bool

    termination::Termination
    mutation::MutationOperator
    crossover::CrossoverOperator

    solver::EvolutionaryAlgorithm
    neighborhood::WeightVectorNeighborhood
    selection::Selection
    variation::Variation
    replacement::Replacement
end

function MOEAD(
    problem::Problem;
    populationSize::Int = 100,
    offspringPopulationSize::Int = 1,
    neighborhoodSize::Int = 20,
    neighborhoodSelectionProbability::Float64 = 0.9,
    maximumNumberOfReplacedSolutions::Int = 2,
    weightVectorDirectory::String = "",
    aggregationFunction = PenaltyBoundaryIntersection(5.0, true),
    sequenceGenerator = DefaultSequenceGenerator(),
    normalize::Bool = true,
    termination::Termination = TerminationByEvaluations(25000),
    crossover::CrossoverOperator = SBXCrossover(probability = 1.0, distributionIndex = 20.0),
    mutation::MutationOperator = PolynomialMutation(probability = 1.0 / numberOfVariables(problem), distributionIndex = 20.0)
)
    moead = new()

    moead.solver = EvolutionaryAlgorithm()
    moead.solver.name = "MOEAD"

    moead.problem = problem
    moead.populationSize = populationSize
    moead.offspringPopulationSize = offspringPopulationSize
    moead.neighborhoodSize = neighborhoodSize
    moead.neighborhoodSelectionProbability = neighborhoodSelectionProbability
    moead.maximumNumberOfReplacedSolutions = maximumNumberOfReplacedSolutions
    moead.weightVectorDirectory = weightVectorDirectory
    moead.aggregationFunction = aggregationFunction
    moead.sequenceGenerator = sequenceGenerator
    moead.normalize = normalize

    moead.crossover = crossover
    moead.mutation = mutation
    moead.termination = termination

    if numberOfObjectives(problem) == 2
        moead.neighborhood = WeightVectorNeighborhood(populationSize, neighborhoodSize)
    else
        moead.neighborhood = WeightVectorNeighborhood(populationSize, numberOfObjectives(problem), neighborhoodSize, weightVectorDirectory)
    end

    moead.variation = CrossoverAndMutationVariation(offspringPopulationSize, crossover, mutation)

    moead.selection = PopulationAndNeighborhoodSelection(
        moead.variation.matingPoolSize,
        sequenceGenerator,
        moead.neighborhood,
        neighborhoodSelectionProbability,
        true
    )

    moead.replacement = MOEADReplacement(
        moead.selection,
        moead.neighborhood,
        aggregationFunction,
        sequenceGenerator,
        maximumNumberOfReplacedSolutions,
        normalize
    )

    return moead
end

function foundSolutions(moead::MOEAD)
    return moead.solver.foundSolutions
end

function optimize!(moead::MOEAD)
    solver = moead.solver
    problem = moead.problem

    solver.solutionsCreation = DefaultSolutionsCreation(problem, moead.populationSize)
    solver.evaluation = SequentialEvaluation(problem)
    solver.termination = moead.termination
    solver.variation = moead.variation
    solver.replacement = moead.replacement
    solver.selection = moead.selection

    return evolutionaryAlgorithm(solver)
end

function name(moead::MOEAD)
    return name(moead.solver)
end

function status(moead::MOEAD)
    return status(moead.solver)
end

function computingTime(moead::MOEAD)
    return status(moead)["COMPUTING_TIME"]
end

function observable(moead::MOEAD)
    return observable(moead.solver)
end
```
## Gemini
```
using Dates

mutable struct MOEAD <: Algorithm
  problem::Problem
  populationSize::Int

  termination::Termination
  mutation::MutationOperator
  crossover::CrossoverOperator

  weightVectorDirectory::String
  sequenceGenerator::SequenceGenerator
  normalize::Bool

  solver::EvolutionaryAlgorithm

  neighborhoodSize = 20
  neighborhoodSelectionProbability = 0.9
  maximumNumberOfReplacedSolutions = 2

  function MOEAD(problem::Problem, populationSize::Int, 
                  crossover::CrossoverOperator, mutation::MutationOperator, 
                  weightVectorDirectory::String, 
                  sequenceGenerator::SequenceGenerator, 
                  normalize=true)
    algorithm = new()
    algorithm.solver = EvolutionaryAlgorithm()
    algorithm.solver.name = "MOEAD"
    algorithm.problem = problem
    algorithm.populationSize = populationSize
    algorithm.mutation = mutation
    algorithm.crossover = crossover
    algorithm.weightVectorDirectory = weightVectorDirectory
    algorithm.sequenceGenerator = sequenceGenerator
    algorithm.normalize = normalize

    if problem.numberOfObjectives == 2
      algorithm.neighborhood = WeightVectorNeighborhood(populationSize, neighborhoodSize)
    else
      try
        algorithm.neighborhood = WeightVectorNeighborhood(populationSize, problem.numberOfObjectives, neighborhoodSize, weightVectorDirectory)
      catch e
        println(e)
      end
    end

    algorithm.selection = PopulationAndNeighborhoodSelection(algorithm.variation.matingPoolSize, 
                                                          sequenceGenerator, algorithm.neighborhood, 
                                                          neighborhoodSelectionProbability, true)

    algorithm.replacement = MOEADReplacement(algorithm.selection, algorithm.neighborhood, 
                                               PenaltyBoundaryIntersection(5.0, normalize), sequenceGenerator, 
                                               maximumNumberOfReplacedSolutions, normalize)

    algorithm.termination = TerminationByEvaluations(25000)
    algorithm.evaluation = SequentialEvaluation(problem)

    return algorithm
  end

  function foundSolutions(moead::MOEAD)
    return moead.solver.foundSolutions
  end

  function optimize!(moead::MOEAD)
    solver = moead.solver
    problem = moead.problem

    populationSize = moead.populationSize
    offspringPopulationSize = 1

    solver.solutionsCreation = DefaultSolutionsCreation(problem, populationSize)
    solver.evaluation = SequentialEvaluation(problem)
    solver.termination = moead.termination
    solver.variation = CrossoverAndMutationVariation(offspringPopulationSize, moead.crossover, moead.mutation)
    solver.replacement = moead.replacement
    solver.selection = moead.selection

    return evolutionaryAlgorithm(solver)
  end

  function name(moead::MOEAD)
    return name(moead.solver)
  end

  function status(moead::MOEAD)
    return status(moead.solver)
  end

  function computingTime(moead::MOEAD)
    return status(moead::MOEAD)["COMPUTING_TIME"]
  end

  function observable(moead::MOEAD)
    return observable(moead.solver)
  end
end

# Helper Types (assuming they exist elsewhere)
struct WeightVectorNeighborhood end
struct SequenceGenerator end
```
## Claude
```
using Dates

mutable struct MOEAD <: Algorithm
    problem::Problem
    populationSize::Int
    neighborhoodSize::Int
    maxReplacedSolutions::Int
    neighborhoodSelectionProbability::Float64
    aggregationFunction::AggregationFunction
    crossover::CrossoverOperator
    mutation::MutationOperator
    sequenceGenerator::SequenceGenerator{Int}
    termination::Termination
    normalize::Bool

    solver::EvolutionaryAlgorithm

    function MOEAD(
        problem::ContinuousProblem;
        populationSize = 100,
        neighborhoodSize = 20,
        maxReplacedSolutions = 2,
        neighborhoodSelectionProbability = 0.9,
        aggregationFunction = PenaltyBoundaryIntersectionAggregation(5.0, true),
        crossover = SBXCrossover(probability = 1.0, distributionIndex = 20.0, bounds = problem.bounds),
        mutation = PolynomialMutation(probability = 1.0 / numberOfVariables(problem), distributionIndex = 20.0, bounds = problem.bounds),
        sequenceGenerator = UniformRandomSequence(1:populationSize),
        termination = TerminationByEvaluations(25000),
        normalize = true
    )
        algorithm = new()
        algorithm.solver = EvolutionaryAlgorithm()
        algorithm.solver.name = "MOEA/D"
        algorithm.problem = problem
        algorithm.populationSize = populationSize
        algorithm.neighborhoodSize = neighborhoodSize
        algorithm.maxReplacedSolutions = maxReplacedSolutions
        algorithm.neighborhoodSelectionProbability = neighborhoodSelectionProbability
        algorithm.aggregationFunction = aggregationFunction
        algorithm.crossover = crossover
        algorithm.mutation = mutation
        algorithm.sequenceGenerator = sequenceGenerator
        algorithm.termination = termination
        algorithm.normalize = normalize

        return algorithm
    end

    function optimise!(moead::MOEAD)
        solver = moead.solver
        problem = moead.problem

        solver.solutionsCreation = DefaultSolutionsCreation(problem, moead.populationSize)
        solver.evaluation = SequentialEvaluation(problem)
        solver.termination = moead.termination

        variation = CrossoverAndMutationVariation(1, moead.crossover, moead.mutation)
        solver.variation = variation

        if problem.numberOfObjectives == 2
            neighborhood = WeightVectorNeighborhood(moead.populationSize, moead.neighborhoodSize)
        else
            neighborhood = WeightVectorNeighborhood(moead.populationSize, problem.numberOfObjectives, moead.neighborhoodSize, "path/to/weight-vectors.dat")
        end

        selection = PopulationAndNeighborhoodSelection(
            variation.matingPoolSize,
            moead.sequenceGenerator,
            neighborhood,
            moead.neighborhoodSelectionProbability,
            true
        )
        solver.selection = selection

        replacement = MOEADReplacement(
            selection,
            neighborhood,
            moead.aggregationFunction,
            moead.sequenceGenerator,
            moead.maxReplacedSolutions,
            moead.normalize
        )
        solver.replacement = replacement

        return evolutionaryAlgorithm(solver)
    end

    function foundSolutions(moead::MOEAD)
        return moead.solver.foundSolutions
    end

    function name(moead::MOEAD)
        return name(moead.solver)
    end

    function status(moead::MOEAD)
        return status(moead.solver)
    end

    function computingTime(moead::MOEAD)
        return status(moead)["COMPUTING_TIME"]
    end

    function observable(moead::MOEAD)
        return observable(moead.solver)
    end
end
```