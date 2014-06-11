# my little reminder for pairs and splom (lattice) plots
## some examples by using data iris
*****

### bare bone pairs plot

```r
pairs(iris[1:4])
```

![plot of chunk unnamed-chunk-1](figure/unnamed-chunk-1.png) 

```r
#equivalent
#pairs(~Sepal.Length+Sepal.Width+Petal.Length+Petal.Width, data=iris)
```
### classify species by color and add a legend (this one quite tricky!)

```r
# bg = background (fill) color for the open plot symbols given by pch = 21
# see ?points, NB: this is not the same setting as par("bg")
# the graphical parameter oma will be set by pairs.default unless supplied as an argument here

pairs(iris[1:4], pch = 21,
      bg = c("red", "green3", "blue")[unclass(iris$Species)],
      oma = c(8,3,2,3)      
      )
# set par  
op<-par(xpd=TRUE)
legend("bottom", as.vector(unique(iris$Species)),
       fill=c("red", "green3", "blue"), ncol=3, bty="n")
```

![plot of chunk unnamed-chunk-2](figure/unnamed-chunk-2.png) 

```r
# back to orgininal par settings
par(op)
# for reference about legend placement 
# http://stackoverflow.com/questions/14948852/how-to-use-the-pairs-function-combined-with-layout-in-r
```
### add correlation coefficients in upper panel with proportional character


```r
#define a correlation function
panel.cor<-function(x,y,...)
{
  par(usr=c(0,1,0,1))
  # this is to set the individual panel block axes limits 0-1
  mycor<-cor(x,y)
  txt<- as.character(format(mycor, digits=2))
  text(0.5, 0.5, txt, cex= 6*abs(mycor))
}
#plot again
pairs(iris[1:4], pch = 21,
      bg = c("red", "green3", "blue")[unclass(iris$Species)],
      oma = c(8,3,2,3), 
      upper.panel=panel.cor
      )
op<-par(xpd=TRUE)
legend("bottom", as.vector(unique(iris$Species)),
       fill=c("red", "green3", "blue"), ncol=3, bty="n")
```

![plot of chunk unnamed-chunk-3](figure/unnamed-chunk-3.png) 

```r
par(op)
```
### add histogram and density line in diagonal, panel smooth in lower panel


```r
# define a histogram function
panel.hist<- function(x,...)
{
  par(usr=c(par("usr")[1:2],0,1.5))
  #usr argument takes values in the form of a vector c(xmin,xmax,ymin,ymax)
  hist(x, prob=TRUE, add=TRUE, col="gray", axes=NULL)
  lines(density(x), col = "red")
}
#plot again
pairs(iris[1:4], pch = 21,
      bg = c("red", "green3", "blue")[unclass(iris$Species)],
      oma = c(8,3,2,3),
      lower.panel=panel.smooth, #see panel.smooth
      upper.panel=panel.cor,
      diag.panel=panel.hist,
      label.pos=0.9, cex.labels=1 #tweak labels
      )
op<-par(xpd=TRUE)
legend("bottom", as.vector(unique(iris$Species)),
       fill=c("red", "green3", "blue"), ncol=3, bty="n")
```

![plot of chunk unnamed-chunk-4](figure/unnamed-chunk-4.png) 

```r
par(op)
```
### splom wtih lattice

```r
require(lattice)
```

```
## Loading required package: lattice
```
### group by species and add legend

```r
splom(~iris[1:4], groups = Species, data = iris, auto.key=TRUE)
```

![plot of chunk unnamed-chunk-6](figure/unnamed-chunk-6.png) 

### add correlation coefficients with proportional character in upper panel, smoothing curve in lower panel, format diagonal labels


```r
panel.corval <- function(x, y,...)
{
  require(grid)
  r <- cor(x, y, use = "complete.obs")
  txt<- as.character(format(r, digits=2))
  grid.text(txt, 0.5, 0.5, gp = gpar(cex = 4*abs(r)))
}

super.sym <- trellis.par.get("superpose.symbol")

splom(iris[1:4], groups = iris$Species, pch=21,
      varnames=c("Sepal\nLength","Sepal\nWidth", "Petal\nLength", "Petal\nWidth"),
      varname.cex=0.9, varname.col="red",
      lower.panel = function(...) {
        panel.xyplot(...)
        panel.loess(..., col = 1, lwd = 2)
      },
      upper.panel = panel.corval,
      key = list(title = "Three Varieties of Iris",
                 columns = 3, 
                 points = list(pch=21, col = super.sym$col[1:3]),
                 text = list(c("Setosa", "Versicolor", "Virginica"))
      )
      ) 
```

```
## Loading required package: grid
```

![plot of chunk unnamed-chunk-7](figure/unnamed-chunk-7.png) 


