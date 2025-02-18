# Oggetto ProcessMetric

* `pid` Integer - Id del processo.
* `tipo` Stringa - Tipo di processo. Uno dei valori seguenti:
  * `Browser`
  * `Tab`
  * `Utilità`
  * `Zygote`
  * `Aiutante sandbox`
  * `GPU`
  * `Plugin Pepper`
  * `Broker del Plugin Pepper`
  * `Sconosciuto`
* `cpu` [CPUUsage](cpu-usage.md) - Uso di CPU del processo.
* `creationTime` Number - Creation time for this process. The time is represented as number of milliseconds since epoch. Since the `pid` can be reused after a process dies, it is useful to use both the `pid` and the `creationTime` to uniquely identify a process.
* `memory` [MemoryInfo](memory-info.md) - Informazioni della memoria per il processo.
* `sandboxed` Boolean (optional) _macOS_ _Windows_ - Whether the process is sandboxed on OS level.
* `integrityLevel` String (optional) _Windows_ - One of the following values:
  * `untrusted`
  * `low`
  * `medium`
  * `high`
  * `sconosciuto`
