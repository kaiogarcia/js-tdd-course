import React, { useState, useEffect } from 'react';
import { View, Text, StyleSheet, ImageBackground, TouchableOpacity } from 'react-native';
import { FontAwesome } from '@expo/vector-icons';
import axios from 'axios';

const HomeScreen = ({ navigation }) => {
  const [backgroundImage, setBackgroundImage] = useState('https://github.githubassets.com/images/modules/logos_page/Octocat.png');
  const [userData, setUserData] = useState(null);

  useEffect(() => {
    // Função para buscar os dados do usuário
    const fetchUserData = async () => {
      try {
        const response = await axios.get('https://api.github.com/search/users?q=rgmellon');
        if (response.data.items && response.data.items.length > 0) {
          // Se existir pelo menos um usuário retornado, atualiza os dados do usuário
          setUserData(response.data.items[0]);
        }
      } catch (error) {
        console.log(error);
      }
    };

    fetchUserData();
  }, []);

  const handleUserClick = () => {
    if (userData) {
      navigation.navigate('Repos', { user: userData });
    }
  };

  return (
    <View style={styles.container}>
      <ImageBackground source={{ uri: backgroundImage }} style={styles.backgroundImage}>
        <TouchableOpacity style={styles.header} onPress={handleUserClick}>
          <FontAwesome name="github" size={32} color="#FFF" />
        </TouchableOpacity>
      </ImageBackground>
    </View>
  );
};

const styles = StyleSheet.create({
  container: {
    flex: 1,
    backgroundColor: '#FDFDFD',
  },
  backgroundImage: {
    flex: 1,
    justifyContent: 'flex-end',
    alignItems: 'center',
  },
  header: {
    backgroundColor: '#040404',
    padding: 16,
    borderRadius: 50,
    marginBottom: 16,
  },
});

export default HomeScreen;
import React, { useState, useEffect } from 'react';
import { View, Text, StyleSheet } from 'react-native';
import AsyncStorage from '@react-native-async-storage/async-storage';

const FavoritesScreen = () => {
  const [favorites, setFavorites] = useState([]);

  useEffect(() => {
    // Função para buscar os usuários favoritos salvos
    const fetchFavorites = async () => {
      try {
        const storedFavorites = await AsyncStorage.getItem('favorites');
        if (storedFavorites) {
          setFavorites(JSON.parse(storedFavorites));
        }
      } catch (error) {
        console.log(error);
      }
    };

    fetchFavorites();
  }, []);

  return (
    <View style={styles.container}>
      <Text style={styles.title}>Usuários Favoritos</Text>
      {favorites.map((user, index) => (
        <Text key={index} style={styles.user}>{user.login}</Text>
      ))}
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
  title: {
    fontSize: 24,
    fontWeight: 'bold',
    marginBottom: 16,
  },
  user: {
    fontSize: 16,
    marginBottom: 8,
  },
});

export default FavoritesScreen;
import React from 'react';
import { NavigationContainer } from '@react-navigation/native';
import { createStackNavigator } from '@react-navigation/stack';
import HomeScreen from './screens/HomeScreen';
import FavoritesScreen from './screens/FavoritesScreen';

const Stack = createStackNavigator();

const App = () => {
  return (
    <NavigationContainer>
      <Stack.Navigator>
        <Stack.Screen name="Home" component={HomeScreen} />
        <Stack.Screen name="Favorites" component={FavoritesScreen} />
      </Stack.Navigator>
    </NavigationContainer>
  );
};

export default App;
