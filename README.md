# test

#input
1, 6, 7, 8, 6, 9,,,,,,, 1, 6, 7, 3, 3, 3,,,,,, 1, 5, 3, 4,,,
8, 6, 7, 19, 9,,,,,,,, 4, 1, 1, 3, 3, 3,,,,,, 4, 1, 3, 4,,,
10, 6, 7, 12,18, 6, 9,,,,, 4, 1, 1, 3, 3, 3,,,,,, 4, 5, 1, 4,,,
14, 6, 17, 28, 6, 9,,,,,,, 4, 6, 7, 3, 1, 2,,,,,, 4, 5, 3, 1,,,
17, 6, 7, 8, 16, 9,,,,,,, 4, 6, 1, 3, 3,,,,,, 4, 5, 1,8,,,
19, 6, 7, 8, 6, 19,,,,,,, 4, 6, 7, 3, 1, 1,,,,,, 1, 1, 3, 4,,,
29, 6, 17, 8, 6, 9,,,,,,, 4, 16, 3, 1, 1,,,,,, 1, 13, 4,,,

#Code
# Setting working directory
setwd("D:/R_workspace")
getwd()
dir()

library(stringr)
options(stringsAsFactors = F)
sample <- read.csv("Sample.txt",header = F)
#View(sample)
# temp <- c(1, 6, 7, 8, 6, 9, NA, NA, NA, NA, NA, NA, 4, 6, 7, 3, 3, 3, NA, NA, NA, NA, NA, 4, 5, 3, NA)
getwd()
finalData <- data.frame(CellID="", TotalSum="", OverSum1="",OverSum2="",stringsAsFactors = FALSE)

#cID<-1
for(cID in 1: nrow(sample)){
  totalSum <- 0
  for(j in 2: ncol(sample)){
    if(!is.na(sample[cID,j])){
      totalSum <- totalSum + sample[cID,j]  
    }
    
  }
  ind <- 0
  ind <- which(!is.na(sample[cID,]))
  # ind <- grep()
  cID <- sample[cID,1]
  ind3 <- NULL
  for(ij in 1:(length(ind)-1)){
    t <- ind[(ij+1)]- ind[ij]
    ind3 <- append(ind3, t)
  }
  # indices <- str_locate_all(paste0(ind3, collapse=""), "1{4,}+") minimum 4 times
  logString <- 0
  indices <- 0
  
  logString <- paste0(ind3, collapse="")
  indices <- str_locate_all(logString, "1{1,}+")
  indices <- as.data.frame(indices)
  print(indices)
  start <- 0
  end <- 0
  startI <- 0
  endI <- 0
  overSum1 <- list(0)
  overSum2 <- list(0)
  vec <- rep(0,nrow(indices)-1)
  overSum1 <- as.list(vec)
    for(i in 2 : nrow(indices)){
      #replicate(nrow(indices)-1,overSum1)
      startI <- indices[i, 1]
      start <- ind[startI]
      print(c("start",start))
      endI <- indices[i, 2]
      End <- ind[endI]+1
      for(l in start : End){
        overSum1[[i-1]] <- overSum1[[i-1]] + sample[cID,l]
        print(overSum1)
      }
    }
    overSum2 <- overSum1[2]
    
    tCount <- (indices[i,2]- indices[i,1])+2
    tempD <- do.call(cbind, list(cID, totalSum,overSum1[1],overSum2))
    colnames(tempD) <- colnames(finalData)
    finalData <- rbind(finalData, tempD)
  }
#Issue ::::Error in .External2(C_writetable, x, file, nrow(x), p, rnames, sep, eol,  : 
#unimplemented type 'list' in 'EncodeElement'
finalData <- finalData[-1, ]
my.df <- data.frame(lapply(finalData, as.character), stringsAsFactors=FALSE)
write.csv(my.df, "FinalData.csv", row.names=F)
