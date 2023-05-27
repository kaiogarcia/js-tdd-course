import AsyncStorage from '@react-native-async-storage/async-storage';

export const storeData = async (key, value) => {
  try {
    await AsyncStorage.setItem(key, JSON.stringify(value));
  } catch (error) {
    console.log(error);
  }
};

export const getData = async (key) => {
  try {
    const value = await AsyncStorage.getItem(key);
    return value !== null ? JSON.parse(value) : null;
  } catch (error) {
    console.log(error);
  }
};

export const removeData = async (key) => {
  try {
    await AsyncStorage.removeItem(key);
  } catch (error) {
    console.log(error);
  }
};


import React, { useEffect, useState } from 'react';
import { View, Text, StyleSheet, FlatList, TouchableOpacity, ImageBackground } from 'react-native';
import { FontAwesome } from '@expo/vector-icons';
import axios from 'axios';
import { storeData, getData, removeData } from '../utils/storage';

const UserList = () => {
  const [users, setUsers] = useState([]);

  useEffect(() => {
    const fetchUsers = async () => {
      try {
        const response = await axios.get('https://api.github.com/search/users?q=rgmellon');
        setUsers(response.data.items);
      } catch (error) {
        console.log(error);
      }
    };

    fetchUsers();
  }, []);

  const handleFavorite = async (user) => {
    const favorites = await getData('favorites');
    if (favorites) {
      const isFavorite = favorites.find((favorite) => favorite.id === user.id);
      if (isFavorite) {
        const updatedFavorites = favorites.filter((favorite) => favorite.id !== user.id);
        await storeData('favorites', updatedFavorites);
      } else {
        await storeData('favorites', [...favorites, user]);
      }
    } else {
      await storeData('favorites', [user]);
    }
  };

  const renderUserItem = ({ item }) => {
    const favorites = getData('favorites');
    const isFavorite = favorites && favorites.find((favorite) => favorite.id === item.id);

    return (
      <View style={styles.userItem}>
        <ImageBackground
          source={{ uri: item.avatar_url }}
          style={styles.avatar}
          resizeMode="cover"
        >
          <Text style={styles.username}>{item.login}</Text>
          <TouchableOpacity onPress={() => handleFavorite(item)} style={styles.favoriteButton}>
            <FontAwesome
              name={isFavorite ? 'heart' : 'heart-o'}
              color={isFavorite ? 'red' : 'white'}
              size={24}
            />
          </TouchableOpacity>
        </ImageBackground>
      </View>
    );
  };

  return (
    <View style={styles.container}>
      {users.length === 0 ? (
        <ImageBackground
          source={{ uri: 'https://github.githubassets.com/images/modules/logos_page/Octocat.png' }}
          style={styles.emptyBackground}
          resizeMode="contain"
        >
          <Text style={styles.emptyText}>Está meio vazio por aqui! Busque por um usuário</Text>
        </ImageBackground>
      ) : (
        <FlatList
          data={users}
          renderItem={renderUserItem}
          keyExtractor={(item) => item.id.toString()}
          contentContainerStyle={styles.userList}
        />
      )}
    </View>
  );
};

const styles = StyleSheet.create({
  container: {
    flex: 1,
    backgroundColor: '#FDFDFD',
  },
  userList: {
    padding: 10,
  },
  userItem: {
    marginBottom: 10,
  },
  avatar: {
    height: 200,
    justifyContent: 'flex-end',
    borderRadius: 10,
    overflow: 'hidden',
  },
  username: {
    fontSize: 16,
    fontWeight: 'bold',
    color: '#040404',
    backgroundColor: 'rgba(0, 0, 0, 0.7)',
    padding: 10,
    paddingBottom: 5,
    color: '#FDFDFD',
  },
  favoriteButton: {
    position: 'absolute',
    top: 10,
    right: 10,
    padding: 10,
  },
  emptyBackground: {
    flex: 1,
    justifyContent: 'center',
    alignItems: 'center',
    padding: 20,
  },
  emptyText: {
    fontSize: 20,
    fontWeight: 'bold',
    color: '#040404',
    textAlign: 'center',
  },
});

export default UserList;

  
  
  import React, { useEffect, useState } from 'react';
import { View, Text, StyleSheet, FlatList, TouchableOpacity } from 'react-native';
import { FontAwesome } from '@expo/vector-icons';
import { getData, removeData } from '../utils/storage';

const FavoritesButton = () => {
  const [favorites, setFavorites] = useState([]);

  useEffect(() => {
    const fetchFavorites = async () => {
      const storedFavorites = await getData('favorites');
      if (storedFavorites) {
        setFavorites(storedFavorites);
      }
    };

    fetchFavorites();
  }, []);

  const handleRemoveFavorite = async (userId) => {
    await removeData('favorites');
    const updatedFavorites = favorites.filter((favorite) => favorite.id !== userId);
    if (updatedFavorites.length > 0) {
      await storeData('favorites', updatedFavorites);
    }
    setFavorites(updatedFavorites);
  };

  const renderFavoriteItem = ({ item }) => {
    return (
      <TouchableOpacity
        style={styles.favoriteItem}
        onPress={() => handleRemoveFavorite(item.id)}
      >
        <Text style={styles.favoriteUsername}>{item.login}</Text>
        <FontAwesome name="trash" color="red" size={24} />
      </TouchableOpacity>
    );
  };

  return (
    <View style={styles.container}>
      {favorites.length === 0 ? (
        <Text style={styles.emptyText}>Nenhum usuário favorito</Text>
      ) : (
        <FlatList
          data={favorites}
          renderItem={renderFavoriteItem}
          keyExtractor={(item) => item.id.toString()}
          contentContainerStyle={styles.favoriteList}
        />
      )}
    </View>
  );
};

const styles = StyleSheet.create({
  container: {
    flex: 1,
    backgroundColor: '#FDFDFD',
    alignItems: 'center',
    justifyContent: 'center',
  },
  favoriteList: {
    padding: 10,
  },
  favoriteItem: {
    flexDirection: 'row',
    alignItems: 'center',
    justifyContent: 'space-between',
    padding: 10,
    marginBottom: 10,
    backgroundColor: '#F5F5F5',
    borderRadius: 5,
    width: '100%',
  },
  favoriteUsername: {
    fontSize: 16,
    fontWeight: 'bold',
    color: '#040404',
  },
  emptyText: {
    fontSize: 20,
    fontWeight: 'bold',
    color: '#040404',
  },
});

export default FavoritesButton;

      
      import React from 'react';
import { NavigationContainer } from '@react-navigation/native';
import { createBottomTabNavigator } from '@react-navigation/bottom-tabs';
import { FontAwesome } from '@expo/vector-icons';
import { createStackNavigator } from '@react-navigation/stack';
import UserList from './components/UserList';
import FavoritesButton from './components/FavoritesButton';

const Tab = createBottomTabNavigator();
const Stack = createStackNavigator();

const HomeScreen = () => (
  <Stack.Navigator>
    <Stack.Screen name="UserList" component={UserList} options={{ headerShown: false }} />
  </Stack.Navigator>
);

const FavoritesScreen = () => (
  <Stack.Navigator>
    <Stack.Screen name="FavoritesButton" component={FavoritesButton} options={{ headerShown: false }} />
  </Stack.Navigator>
);

const App = () => (
  <NavigationContainer>
    <Tab.Navigator
      screenOptions={({ route }) => ({
        tabBarIcon: ({ color, size }) => {
          let iconName;
          if (route.name === 'Home') {
            iconName = 'home';
          } else if (route.name === 'Favorites') {
            iconName = 'heart';
          }
          return <FontAwesome name={iconName} size={size} color={color} />;
        },
      })}
      tabBarOptions={{
        activeTintColor: '#040404',
        inactiveTintColor: '#D6D6D6',
        style: {
          backgroundColor: '#F5F5F5',
        },
      }}
    >
      <Tab.Screen name="Home" component={HomeScreen} />
      <Tab.Screen name="Favorites" component={FavoritesScreen} />
    </Tab.Navigator>
  </NavigationContainer>
);

export default App;
