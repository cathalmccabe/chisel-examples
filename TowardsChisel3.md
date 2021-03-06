# Towards Chisel 3

I will take notes along the way when moving this example collection
to Chisel 3.

See also [Chisel2 vs. Chisel3](https://github.com/freechipsproject/chisel3/wiki/Chisel3-vs-Chisel2).

Not all steps will be needed for all projects. And maybe your project is
more advanced and needs additional steps.

## Preparation Work

 * Move to the latest version of Chisel2 (2.2.38) and check that everything works as expected
 * Mandatory changes:
   * Use ```Wire()```
 * Start to use some actual features, such as:
   * Input/Output is already available, but not UInt(8.W), so use UInt(width = 8) for now
   * nicer constants are available, such as 0.U
 * Install verilator, if not yet done

## The Switch

### Start with the Hello World

We want to get the LED blinking on an FPGA board.

 * Jump in and change `build.sbt` with a few more lines

```
resolvers ++= Seq(
  Resolver.sonatypeRepo("snapshots"),
  Resolver.sonatypeRepo("releases")
)

libraryDependencies += "edu.berkeley.cs" %% "chisel3" % "3.0-SNAPSHOT"
```

or better:
```
libraryDependencies += "edu.berkeley.cs" %% "chisel3" % "3.1.2"
```

 * first surprise: no more `chiselMain()`
 * Found the way to generate Verilog in the FAQ
```
object HelloWorld extends App {
  chisel3.Driver.execute(args, () => new HelloWorld)
}
```
 * To continue with the compatibility package `Chisel` I imported only the driver
```
import chisel3.Driver
```
 * Now generating hardware. Next surprise when trying to synthesize: the
name of the clock net was changed from `clk` to `clock`. No big deal.
 * Synthesize, configure the FPGA, and the LED blinks in Chisel 3
 * Change to chisel3 package

To be continued with:
 * The other examples, which also include testers

## Testing

Chisel testing has been moved to its own library and needs to be included
in the build.sbt.

## Compatibility Issues

Use latest Chisel 2 version (2.2.38)

according to Jim following should be kept in Chisel 2 and works with the Chisel 3 compatibility layer:

```
val dout = UInt(OUTPUT, 32)
```

then the change can be made on a file by file base.

Input and Output are added the Chisel 3.1.2 compatibility package,
so one can move to Input/Output/IO in Chisel 2 and switch.

Wires need to be wrapped into a ```Wire()```, even in the compatibility
mode. But this can also be done in 2.2.38.

One issues in the examples:

```
Input(gen.clone)
```
Solution: use ```cloneType```

Probably we can drop most clone() methods in the OCP code on Patmos?
Need to check how this works now and what e.g., a Vec uses (clone() or cloneType())?
