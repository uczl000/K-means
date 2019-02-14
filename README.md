# K-means
from scipy.io import arff
import pandas as pd
import numpy as np
from numpy import array
get_ipython().run_line_magic('matplotlib', 'inline')
from copy import deepcopy
from matplotlib import pyplot as plt
originaldata = arff.loadarff('segment.arff')
df = pd.DataFrame(originaldata[0])





dataframe=df.drop(['region-pixel-count'],axis=1)
data=dataframe.values





#data normalization
main=data[:,:-1]
new=[]
for i in range(18):
    m_main=np.mean(main[:,i])
    d_main=np.std(main[:,i])
    n=(main[:,i]-m_main)/d_main
    new.append(n)
n_data=np.asarray(new)
norm_data=np.transpose(n_data)       
print(norm_data)





M=[775, 1020, 200, 127, 329, 1626, 1515, 651, 658, 328, 1160, 108, 422, 88, 105, 261, 212,
1941, 1724, 704, 1469, 635, 867, 1187, 445, 222, 1283, 1288, 1766, 1168, 566, 1812, 214,
53, 423, 50, 705, 1284, 1356, 996, 1084, 1956, 254, 711, 1997, 1378, 827, 1875, 424,
1790, 633, 208, 1670, 1517, 1902, 1476, 1716, 1709, 264, 1, 371, 758, 332, 542, 672, 483,
65, 92, 400, 1079, 1281, 145, 1410, 664, 155, 166, 1900, 1134, 1462, 954, 1818, 1679,
832, 1627, 1760, 1330, 913, 234, 1635, 1078, 640, 833, 392, 1425, 610, 1353, 1772, 908,
1964, 1260, 784, 520, 1363, 544, 426, 1146, 987, 612, 1685, 1121, 1740, 287, 1383, 1923,
1665, 19, 1239, 251, 309, 245, 384, 1306, 786, 1814, 7, 1203, 1068, 1493, 859, 233, 1846,
1119, 469, 1869, 609, 385, 1182, 1949, 1622, 719, 643, 1692, 1389, 120, 1034, 805, 266,
339, 826, 530, 1173, 802, 1495, 504, 1241, 427, 1555, 1597, 692, 178, 774, 1623, 1641,
661, 1242, 1757, 553, 1377, 1419, 306, 1838, 211, 356, 541, 1455, 741, 583, 1464, 209,
1615, 475, 1903, 555, 1046, 379, 1938, 417, 1747, 342, 1148, 1697, 1785, 298, 1485,
945, 1097, 207, 857, 1758, 1390, 172, 587, 455, 1690, 1277, 345, 1166, 1367, 1858, 1427,
1434, 953, 1992, 1140, 137, 64, 1448, 991, 1312, 1628, 167, 1042, 1887, 1825, 249, 240,
524, 1098, 311, 337, 220, 1913, 727, 1659, 1321, 130, 1904, 561, 1270, 1250, 613, 152,
1440, 473, 1834, 1387, 1656, 1028, 1106, 829, 1591, 1699, 1674, 947, 77, 468, 997, 611,
1776, 123, 979, 1471, 1300, 1007, 1443, 164, 1881, 1935, 280, 442, 1588, 1033, 79, 1686,
854, 257, 1460, 1380, 495, 1701, 1611, 804, 1609, 975, 1181, 582, 816, 1770, 663, 737,
1810, 523, 1243, 944, 1959, 78, 675, 135, 1381, 1472]




def centroids(k,starter):
    a=starter*k
    b=(starter+1)*k
    m=M[a:b]
    m_array=np.asarray(m)
    C_original=norm_data[m_array,:]
    return C_original 





def dist(C_original):
    distances=np.zeros((len(norm_data),len(C_original)))
    for i in range(len(C_original)):
        distances[:,i]=np.sum(np.square(norm_data-C_original[i]),axis=1)
    return distances





def K_means(C_original):
    error = 100
    t=0
    C=C_original
    while error !=0 and t<50:
        distances=dist(C)
        cluster = np.argmin(distances,axis=1)
        C_change=np.zeros((len(C_original),18))
        for j in range(len(C_original)):
            C_change[j]=np.mean(norm_data[cluster==j],axis=0)
       # C_old=deepcopy(C_new)
        error= np.sum(np.square(C_change - C))
        C=C_change
        t=t+1
    return C, cluster





def mean_sse(k):
    matrix=np.zeros(25)
    for starter in range(25):
        C_original=centroids(k,starter)
        C,cluster=K_means(C_original)
        SSE_all=np.zeros(len(C_original))
        for j in range(len(C_original)):
            SSE_all[j]=np.sum(np.square(norm_data[cluster==j]-C[j]))
            SSE=np.sum(SSE_all)  
        matrix[starter]=SSE
    u_k=np.mean(matrix)
    
    return u_k





def std_sse(k):
    matrix=np.zeros(25)
    for starter in range(25):
        C_original=centroids(k,starter)
        C,cluster=K_means(C_original)
        SSE_all=np.zeros(len(C_original))
        for j in range(len(C_original)):
            SSE_all[j]=np.sum(np.square(norm_data[cluster==j]-C[j]))
            SSE=np.sum(SSE_all)  
        matrix[starter]=SSE
    
    sigma_k=np.std(matrix)
    return sigma_k





Y=np.zeros(12)
for k in range(1,13):
    Y[k-1]=mean_sse(k)
print(Y)





Z=np.zeros(12)
for k in range(1,13):
    Z[k-1]=std_sse(k)
print(Z)





plt.errorbar([k for k in range(1,13)], Y, yerr=2*Z) 
plt.show()



