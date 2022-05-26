Abre uma transação e executa o rollback no final de cada teste.

Código para MSTest:
````
using System;
using System.Transactions;
using Microsoft.VisualStudio.TestTools.UnitTesting;

namespace Sefaz.Testes
{
    [TestClass]
    public class TransactionWrappedTest
    {
        private TransactionScope _transactionScope;

        [TestInitialize]
        public void TestInitialize()
        {
            var transactionOptions = new TransactionOptions
            {
                IsolationLevel = IsolationLevel.ReadUncommitted,
                Timeout = new TimeSpan(0, 10, 0)
            };

            _transactionScope = new TransactionScope(
                TransactionScopeOption.RequiresNew, 
                transactionOptions, 
                TransactionScopeAsyncFlowOption.Enabled);
        }

        [TestCleanup]
        public void TestCleanup()
        {
            _transactionScope.Dispose();
        }
    }
}
````