# Word-Guessing-shopping-Cart
As Reference


using MSD.MJ.Tutorial.Models;
using MSD.MJ.Tutorial.WordGuessing1.Models;
using System;
using System.Collections.Generic;
using System.IO;
using System.Linq;
using System.Reflection;
using System.Text;
using System.Threading.Tasks;


namespace MSD.MJ.Tutorial.WordGuessing1
{
    public class WordGuessingApp : ITutorialApp
    {
        public WordGuessingApp()
        {
            AvailableItems = new List<WordGuessingItem>();
            AnsweredItems = new List<WordGuessingItem>();
        }

        private List<WordGuessingItem> AvailableItems { get; set; }
        private List<WordGuessingItem> AnsweredItems { get; set; }
        private string CurrentAnswer = string.Empty;
        public int Tries { get; set; }
        public bool IsAnswering { get; set; } = true;

        public void Run()
        {
            Console.Clear();
            Console.WriteLine("Word Guessing");
            SetAvailableItems();
            DisplayQuestion(GetNextQuestion());
        }

        private void SetAvailableItems()
        {
            var returnList = new List<WordGuessingItem>();

            string csvPath = @"C:\Users\mllrdev11\Documents\Visual Studio 2015\Projects\MSD.MJ.Tutorial\MSD.MJ.Tutorial\WordGuessingItem.txt";

            StreamReader reader = new StreamReader(csvPath);
            string line = string.Empty;

            //"question","answer"
            while ((line = reader.ReadLine()) != null)
            {
                var csvRecord = line.Split(',');
                returnList.Add(new WordGuessingItem
                {
                    Question = csvRecord[0],
                    Answer = csvRecord[1]
                });
            }

            AvailableItems = returnList;
        }

        private void DisplayQuestion(WordGuessingItem item)
        {
            if (Tries < 3)
            {
                while (IsAnswering)
                {
                    Console.WriteLine(item.Question);

                    if (CurrentAnswer.Length == 0)
                    {
                        for (int i = 0; i < item.Answer.Length; i++)
                        {
                            CurrentAnswer += "_";
                        }
                    }

                    Console.WriteLine(CurrentAnswer);
                    ReadAnswer(item.Answer);
                }

                AvailableItems.Remove(item);
                AnsweredItems.Add(item);

                if (AvailableItems.Count() > 0)
                {
                    IsAnswering = true;
                    CurrentAnswer = string.Empty;
                    DisplayQuestion(GetNextQuestion());
                }
                else
                {
                    IsAnswering = false;
                }
            }
        }

        private void ReadAnswer(string answer)
        {
            var input = Console.ReadLine();
            var currentAnswerAsArray = CurrentAnswer.ToArray();

            if (answer.ToUpper().Contains(input.ToUpper()))
            {
                for(int i = 0; i< answer.Length; i++)
                {
                    if (answer[i].ToString().ToUpper().Equals(input.ToUpper()))
                    {
                        currentAnswerAsArray[i] = input[0];
                    }
                }

                CurrentAnswer = new string(currentAnswerAsArray);
                IsAnswering = CurrentAnswer.Contains("_");
                if (!IsAnswering)
                {                    
                    Console.WriteLine($"Congrats: The answer is {CurrentAnswer}");
                }
            }
            else
            {
                Tries++;
                Console.WriteLine("Errr... Try again");
                if (Tries == 3)
                {
                    IsAnswering = false;
                    Console.WriteLine("Game Over");
                }
            }
        }

        private WordGuessingItem GetNextQuestion()
        {
            Random random = new Random();
            int toSkip = random.Next(0, AvailableItems.Count());

            return AvailableItems.Skip(toSkip).Take(1).First();
        }
    }
}
