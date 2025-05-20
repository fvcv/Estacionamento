using System;
using System.Collections.Generic;
using System.Globalization;

namespace SistemaEstacionamento
{
    public class Veiculo
    {
        public string Placa { get; set; }
        public DateTime HoraEntrada { get; set; }
        public DateTime? HoraSaida { get; set; }
        public double ValorPago { get; set; }

        public Veiculo(string placa)
        {
            Placa = placa;
            HoraEntrada = DateTime.Now;
            HoraSaida = null;
            ValorPago = 0;
        }
    }

    public class Estacionamento
    {
        private List<Veiculo> veiculosEstacionados;
        private List<Veiculo> historicoVeiculos;
        private readonly decimal valorHora = 5.00m;
        private readonly decimal valorAdicionalPorHora = 2.00m;
        private readonly int toleranciaMinutos = 15;

        public Estacionamento()
        {
            veiculosEstacionados = new List<Veiculo>();
            historicoVeiculos = new List<Veiculo>();
        }

        public void AdicionarVeiculo()
        {
            Console.Clear();
            Console.WriteLine("=== Adicionar Veículo ===");
            Console.Write("Digite a placa do veículo: ");
            string placa = Console.ReadLine().ToUpper();

            if (veiculosEstacionados.Exists(v => v.Placa == placa))
            {
                Console.WriteLine("\nVeículo já está estacionado!");
            }
            else
            {
                Veiculo novoVeiculo = new Veiculo(placa);
                veiculosEstacionados.Add(novoVeiculo);
                Console.WriteLine($"\nVeículo {placa} adicionado com sucesso às {novoVeiculo.HoraEntrada:HH:mm}.");
            }

            Console.WriteLine("\nPressione qualquer tecla para continuar...");
            Console.ReadKey();
        }

        public void RemoverVeiculo()
        {
            Console.Clear();
            Console.WriteLine("=== Remover Veículo ===");
            
            if (veiculosEstacionados.Count == 0)
            {
                Console.WriteLine("Não há veículos estacionados.");
                Console.WriteLine("\nPressione qualquer tecla para continuar...");
                Console.ReadKey();
                return;
            }

            Console.Write("Digite a placa do veículo a ser removido: ");
            string placa = Console.ReadLine().ToUpper();

            Veiculo veiculo = veiculosEstacionados.Find(v => v.Placa == placa);

            if (veiculo == null)
            {
                Console.WriteLine("\nVeículo não encontrado no estacionamento.");
            }
            else
            {
                veiculo.HoraSaida = DateTime.Now;
                TimeSpan tempoEstacionado = veiculo.HoraSaida.Value - veiculo.HoraEntrada;

                // Cálculo do valor a ser pago
                decimal valorTotal = 0;

                if (tempoEstacionado.TotalMinutes <= toleranciaMinutos)
                {
                    valorTotal = 0;
                }
                else
                {
                    // Arredonda para cima a cada hora cheia
                    int horas = (int)Math.Ceiling(tempoEstacionado.TotalHours);
                    valorTotal = valorHora + (horas - 1) * valorAdicionalPorHora;
                }

                veiculo.ValorPago = (double)valorTotal;

                Console.WriteLine("\n=== Recibo ===");
                Console.WriteLine($"Placa: {veiculo.Placa}");
                Console.WriteLine($"Entrada: {veiculo.HoraEntrada:dd/MM/yyyy HH:mm}");
                Console.WriteLine($"Saída: {veiculo.HoraSaida:dd/MM/yyyy HH:mm}");
                Console.WriteLine($"Tempo estacionado: {tempoEstacionado.Hours}h {tempoEstacionado.Minutes}m");
                Console.WriteLine($"Valor a pagar: R$ {valorTotal.ToString("F2", CultureInfo.InvariantCulture)}");

                historicoVeiculos.Add(veiculo);
                veiculosEstacionados.Remove(veiculo);
            }

            Console.WriteLine("\nPressione qualquer tecla para continuar...");
            Console.ReadKey();
        }

        public void ListarVeiculosEstacionados()
        {
            Console.Clear();
            Console.WriteLine("=== Veículos Estacionados ===");

            if (veiculosEstacionados.Count == 0)
            {
                Console.WriteLine("Não há veículos estacionados no momento.");
            }
            else
            {
                foreach (var veiculo in veiculosEstacionados)
                {
                    Console.WriteLine($"Placa: {veiculo.Placa} | Entrada: {veiculo.HoraEntrada:dd/MM/yyyy HH:mm}");
                }
            }

            Console.WriteLine("\nPressione qualquer tecla para continuar...");
            Console.ReadKey();
        }

        public void ListarHistorico()
        {
            Console.Clear();
            Console.WriteLine("=== Histórico de Veículos ===");

            if (historicoVeiculos.Count == 0)
            {
                Console.WriteLine("Nenhum veículo passou pelo estacionamento ainda.");
            }
            else
            {
                foreach (var veiculo in historicoVeiculos)
                {
                    Console.WriteLine($"Placa: {veiculo.Placa} | " +
                                      $"Entrada: {veiculo.HoraEntrada:dd/MM/yyyy HH:mm} | " +
                                      $"Saída: {veiculo.HoraSaida:dd/MM/yyyy HH:mm} | " +
                                      $"Valor pago: R$ {veiculo.ValorPago.ToString("F2", CultureInfo.InvariantCulture)}");
                }
            }

            Console.WriteLine("\nPressione qualquer tecla para continuar...");
            Console.ReadKey();
        }

        public void ExibirMenu()
        {
            while (true)
            {
                Console.Clear();
                Console.WriteLine("=== Sistema de Estacionamento ===");
                Console.WriteLine("1. Adicionar Veículo");
                Console.WriteLine("2. Remover Veículo");
                Console.WriteLine("3. Listar Veículos Estacionados");
                Console.WriteLine("4. Listar Histórico de Veículos");
                Console.WriteLine("5. Sair");
                Console.Write("\nEscolha uma opção: ");

                string opcao = Console.ReadLine();

                switch (opcao)
                {
                    case "1":
                        AdicionarVeiculo();
                        break;
                    case "2":
                        RemoverVeiculo();
                        break;
                    case "3":
                        ListarVeiculosEstacionados();
                        break;
                    case "4":
                        ListarHistorico();
                        break;
                    case "5":
                        return;
                    default:
                        Console.WriteLine("Opção inválida. Tente novamente.");
                        Console.WriteLine("\nPressione qualquer tecla para continuar...");
                        Console.ReadKey();
                        break;
                }
            }
        }
    }

    class Program
    {
        static void Main(string[] args)
        {
            Estacionamento estacionamento = new Estacionamento();
            estacionamento.ExibirMenu();
        }
    }
}
